# PyQt6 Module Development Playbook

**Applies to:** Tag Writer, Audio Tag Writer, HST-Metadata, QR Code Generator, and all future MCOS GUI modules  
**Last updated:** 2026-06-19

---

## The Shared Module Ecosystem

All SynchroSoft PyQt6 apps draw from a set of shared modules that live as sibling directories under `~/Projects/`. These modules solve cross-platform Qt problems once; every app consumes them via a thin wrapper.

| Module | Location | Purpose |
|---|---|---|
| Icon Manager Module | `~/Projects/Icon_Manager_Module/` | Cross-platform icon loading, Windows taskbar / AppUserModelID fix |
| ThemeManager | `~/Projects/ThemeManager/` | 6-theme `QPalette` system, `QSettings` persistence |
| pyqt-app-info | `~/Projects/pyqt-app-info/` | App identity dataclass, About dialog |
| version-checker-module | `~/Projects/version-checker-module/` | Update checking |

None of these are installed packages. Each app adds the module's directory to `sys.path` at module-import time using a path-setup block at the top of whatever file first needs it.

---

## Lessons Learned

### 1. Use shared modules from day one — never prototype locally

Both the QR Code app and Tag Writer started with a local `theme.py` that duplicated what ThemeManager already provides — hundreds of lines of palette dicts and CSS generation. The QR app also started with a hand-rolled `get_app_icon()` instead of using the Icon Manager Module. In both cases the migration cost (two sessions, two version bumps each) was entirely avoidable.

**Rule:** Before writing any icon, theme, About dialog, or update-check code, check the shared module ecosystem first. Write a thin wrapper; never a reimplementation.

### 2. Module initialization order is load-bearing

The correct startup sequence for every app is strict and must not be reordered:

```
1. Module-level path setup + imports
      ↳ IMM import fires _init_win32() here — before QApplication exists
2. QApplication(sys.argv)
3. app.setOrganizationName() / app.setApplicationName()
      ↳ This scopes QSettings — must happen before any QSettings read
4. app.setWindowIcon(get_app_icon())
5. MainWindow.__init__()
      ↳ Reads QSettings for persisted theme — org/app name must already be set
6. window.show()
7. _app_icons.set_taskbar_icon(window, APP_USER_MODEL_ID)
      ↳ Requires a visible window with a valid native handle (winId())
```

Any deviation causes silent failures: wrong taskbar icon, QSettings reading the wrong scope, or the wrong theme restored on first launch.

### 3. `APP_USER_MODEL_ID` belongs in `constants.py`

Tag Writer hardcodes `APP_USER_MODEL_ID = "SynchroSoft.TagWriter.TW.0.2.5"` in constants. That is the right location — the `.spec` file and any future Windows installer need to reference it from one place. Convention: `"SynchroSoft.<AppShortName>"` (version-free so it stays stable across releases).

### 4. `resources/icons/` is always generated, never hand-crafted

Run `generate_icons.py` from the Icon Manager Module once when the project is created. It takes a single high-resolution PNG and produces the full set: `app_16x16.png` through `app_256x256.png`, `app.png`, `app.ico`, `app.icns`. Never edit these files by hand.

The `.gitignore` always needs a negation rule alongside the standard `*.png` exclusion:

```gitignore
__pycache__/
*.png
!resources/icons/*.png
```

### 5. `theme.py` is always a thin wrapper — 25 lines, not 250

The correct pattern, established in the QR Code app and to be followed by all future modules:

```python
# theme.py
_TM_PATH = os.path.expanduser("~/Projects/ThemeManager")
if os.path.isdir(_TM_PATH) and _TM_PATH not in sys.path:
    sys.path.insert(0, _TM_PATH)

from theme_manager import get_theme_registry, get_fusion_palette, detect_system_theme  # noqa: F401

DEFAULT_THEME = "light"
_DARK_THEMES = {"dark", "solarized_dark", "dracula"}

def is_dark_theme(name: str) -> bool:
    return name in _DARK_THEMES

def canvas_color(name: str) -> str:
    theme = get_theme_registry().get_theme(name)
    return theme.ui_palette.base_color if theme else "#ffffff"
```

The `canvas_color()` helper is needed only by apps with a content preview area (QR Code Generator, HST-Metadata). Apps without one omit it.

### 6. `apply_theme()` uses `QPalette`, not `setStyleSheet()`

ThemeManager's `get_fusion_palette()` returns a `QPalette` configured for Qt's Fusion style. The correct application:

```python
def apply_theme(self):
    QApplication.instance().setPalette(get_fusion_palette(self.current_theme))
    QSettings().setValue("theme/current", self.current_theme)
    if hasattr(self, "dark_mode_action"):
        self.dark_mode_action.setChecked(is_dark_theme(self.current_theme))
```

Never call `app.setStyleSheet()` for whole-app theming — it fights the palette and produces inconsistent results on different widget types. Widget-scoped stylesheets (e.g., the scroll area canvas color) remain fine.

### 7. Theme persistence is two lines

Save on change (in `apply_theme()`):
```python
QSettings().setValue("theme/current", self.current_theme)
```

Load on startup (in `MainWindow.__init__()`):
```python
saved = QSettings().value("theme/current", DEFAULT_THEME)
self.current_theme = saved if get_theme_registry().get_theme(saved) else DEFAULT_THEME
```

The validation guard (`get_theme_registry().get_theme(saved)`) protects against stale keys if the theme set changes between versions.

### 8. PySide6 compatibility is already handled

MCOS targets PySide6; the sibling standalone apps use PyQt6. Both ThemeManager and the Icon Manager Module detect the available framework at import time (`_QT_FRAMEWORK` flag). The thin wrappers in `theme.py` and `main.py` need no changes. Only the `from PyQt6...` imports in the mixin files need updating when porting a standalone module into the MCOS desktop client.

---

## Standard Project Structure

```
<module>/
  <module>_gui.py              # thin launcher: sys.path setup + call main()
  ICON_<module>.png            # source icon (512×512+), checked into git
  resources/
    icons/
      app.ico                  # generated — Windows multi-resolution
      app.icns                 # generated — macOS
      app.png                  # generated — Linux default
      app_16x16.png            # generated — full resolution set
      app_24x24.png
      app_32x32.png
      app_48x48.png
      app_64x64.png
      app_128x128.png
      app_256x256.png
  src/
    <module>_gui/
      __init__.py
      constants.py             # APP_NAME, APP_SHORT_NAME, APP_VERSION,
                               # APP_TIMESTAMP, APP_ORGANIZATION, APP_USER_MODEL_ID
      main.py                  # MainWindow + main(); IMM + QSettings wiring
      menu.py                  # MenuMixin
      file_ops.py              # FileOpsMixin
      view.py                  # ViewMixin (zoom + theme)
      help.py                  # HelpMixin (README, Changelog, About)
      theme.py                 # thin ThemeManager wrapper
      <domain>.py              # app-specific, Qt-free business logic
  tests/
  CHANGELOG.md
  .gitignore
```

---

## Day-0 Checklist for a New Module

Work through this list in order. The goal is a window that opens with the correct icon and default theme before any domain logic is written.

- [ ] **Create source icon** — `ICON_<module>.png`, 512×512 or larger
- [ ] **Generate icon set**
  ```
  python ~/Projects/Icon_Manager_Module/generate_icons.py ICON_<module>.png
  ```
- [ ] **Fill `constants.py`** — all six identity fields; set `APP_USER_MODEL_ID = "SynchroSoft.<AppShortName>"`
- [ ] **Copy `theme.py` wrapper** — identical across modules; only `canvas_color()` is optional
- [ ] **Scaffold `main.py`**
  - IMM path setup + `IconLoader` at module level
  - ThemeManager import (via `theme.py`)
  - `QSettings` load in `MainWindow.__init__()`
  - `set_taskbar_icon()` after `window.show()`
- [ ] **Scaffold `help.py`** — update `AppIdentity` fields and features list; rest is boilerplate
- [ ] **Initial commit** — project compiles, window opens, icon and default theme are correct
- [ ] **Add `.gitignore`** — `__pycache__/`, `*.png`, `!resources/icons/*.png`

---

## Per-Module Development Cycle

1. Implement domain logic in `<domain>.py` (keep Qt-free where possible)
2. Add UI in the appropriate mixin file
3. Wire menu actions in `menu.py`
4. **Verify icon** — taskbar, title bar, Alt-Tab switcher, About dialog
5. **Verify all 6 themes** — cycle through each; confirm dark-mode toggle; restart and confirm persistence
6. Bump `APP_VERSION` and `APP_TIMESTAMP` in `constants.py` and launcher header comment
7. Update `CHANGELOG.md` before committing the bump

---

## Integrating a Standalone Module into MCOS

1. **Framework swap** — change `from PyQt6...` to `from PySide6...` in all mixin files; `theme.py` and IMM wrappers are already framework-agnostic
2. **QSettings namespace** — prefix keys with the module name to avoid collisions with other MCOS modules: `"qr_module/theme/current"`, `"qr_module/last_dir"`, etc.
3. **Entry point** — expose `main()` (or a `create_widget()` factory) so MCOS can embed the module as a tab or launch it as a managed subprocess
4. **Icon** — MCOS will supply the application-level icon; the module's `resources/icons/` set is still used for any module-specific dialogs or system tray entries

---

## Apps Still to Migrate

| App | IMM | ThemeManager | pyqt-app-info |
|---|---|---|---|
| QR Code Generator | ✅ v0.2.1 | ✅ v0.2.2 | ✅ |
| Tag Writer | ❌ local | ❌ local (8-theme CSS) | — |
| Audio Tag Writer | ❌ local | ❌ local | — |
| HST-Metadata | — | — | — |
