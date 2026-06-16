# Use Cases — Phase 1: Media Module

---

## UC-MED-001 — Add a Primary Image to an Object

**Actor:** Staff  
**Phase:** 1 — Core Framework  
**Module:** Media

### Preconditions
- UC-OBJ-001 completed — object "2024.001.002" exists with no media attached.

### Test Data
| Field             | Value |
| :---------------- | :---- |
| file              | post_office_front.tif |
| file_type         | image/tiff |
| caption           | Front facade, looking east |
| is_primary        | TRUE |

### Steps
1. Open object "2024.001.002".
2. Click **Add Image**.
3. Select file "post_office_front.tif".
4. Enter caption.
5. Check **Set as Primary Image**.
6. Click **Save**.

### Expected Outcome
- One new row in `media`:
  - `object_id` matches object "2024.001.002"
  - `file_path` set to relative path under media root
  - `original_filename = 'post_office_front.tif'`
  - `file_type = 'image/tiff'`
  - `file_size` populated (bytes)
  - `is_primary = TRUE`
  - `sort_order = 0`
  - `created_at` set to now
- Image displayed as the object's primary image.
- `audit_log`: `action = 'CREATE'`, `table_name = 'media'`

### Schema Coverage
- `media`: INSERT
- `audit_log`: INSERT

---

## UC-MED-002 — Add a Second Non-Primary Image

**Actor:** Staff  
**Phase:** 1 — Core Framework  
**Module:** Media

### Preconditions
- UC-MED-001 completed — object "2024.001.002" has exactly one primary image (post_office_front.tif).

### Test Data
| Field             | Value |
| :---------------- | :---- |
| file              | post_office_rear.jpg |
| file_type         | image/jpeg |
| caption           | Rear loading entrance |
| is_primary        | FALSE |

### Steps
1. Open object "2024.001.002".
2. Click **Add Image**.
3. Select "post_office_rear.jpg"; enter caption; leave **Primary** unchecked.
4. Click **Save**.

### Expected Outcome
- Second row in `media` with `is_primary = FALSE`, `sort_order = 0`.
- The first image remains primary.
- Both images visible in the object's media gallery.

### Schema Coverage
- `media`: INSERT

---

## UC-MED-003 — [NEGATIVE] Attempt to Add a Second Primary Image

**Actor:** Staff  
**Phase:** 1 — Core Framework  
**Module:** Media

### Preconditions
- UC-MED-001 completed — object "2024.001.002" has one image with `is_primary = TRUE`.

### Steps
1. Click **Add Image** on object "2024.001.002".
2. Select a new image file.
3. Check **Set as Primary Image**.
4. Click **Save**.

### Expected Outcome
- INSERT is blocked by the partial unique index `(object_id) WHERE is_primary = TRUE`.
- Application displays: *"This object already has a primary image. Unset the current primary image first, or add this image as non-primary."*
- No new row inserted.

### Schema Coverage
- `media`: INSERT (blocked by partial unique index on `(object_id) WHERE is_primary = TRUE`)

---

## UC-MED-004 — Change Which Image Is Primary

**Actor:** Staff  
**Phase:** 1 — Core Framework  
**Module:** Media

### Preconditions
- UC-MED-001 and UC-MED-002 completed — object "2024.001.002" has two images: "post_office_front.tif" (primary) and "post_office_rear.jpg" (non-primary).

### Steps
1. Open the media gallery for object "2024.001.002".
2. Select "post_office_rear.jpg".
3. Click **Set as Primary**.
4. Confirm.

### Expected Outcome
- The rear image (post_office_rear.jpg) is now displayed as the primary image in the gallery and on the object detail view.
- The front image (post_office_front.tif) is shown as non-primary.
- No moment exists where both images show as primary, and no moment where neither is primary — the swap is atomic.
- `audit_log` records two UPDATE entries for `media` (one for each image's `is_primary` change).

### Schema Coverage
- `media`: UPDATE ×2 (within a single transaction)
- `audit_log`: INSERT ×2

---

## UC-MED-005 — Reorder Images Using sort_order

**Actor:** Staff  
**Phase:** 1 — Core Framework  
**Module:** Media

### Preconditions
- UC-MED-001 and UC-MED-002 completed — object "2024.001.002" has at least two images.

### Steps
1. Open the media gallery for object "2024.001.002".
2. Drag the rear image to position 1 (before the front image).
3. Save the new order.

### Expected Outcome
- `sort_order` values updated to reflect the new sequence (e.g. rear image `sort_order = 0`, front image `sort_order = 1`).
- Gallery renders images in the new order.

### Schema Coverage
- `media`: UPDATE (`sort_order`)
- `audit_log`: INSERT

---

## UC-MED-006 — Delete a Single Image

**Actor:** Registrar  
**Phase:** 1 — Core Framework  
**Module:** Media

### Preconditions
- UC-MED-004 completed — object "2024.001.002" has two images; post_office_rear.jpg is currently primary, post_office_front.tif is non-primary. The non-primary front image will be deleted.

### Steps
1. Open the media gallery for object "2024.001.002".
2. Select "post_office_rear.jpg".
3. Click **Delete Image** and confirm.

### Expected Outcome
- The `media` row for the rear image is deleted.
- The remaining image (front) is unchanged and still marked primary.
- `audit_log`: `action = 'DELETE'`, `table_name = 'media'`, `changed_fields` contains a snapshot of the deleted row.

### Schema Coverage
- `media`: DELETE
- `audit_log`: INSERT

---

## UC-MED-007 — Delete an Object and Verify Media Cascade

**Actor:** Admin  
**Phase:** 1 — Core Framework  
**Module:** Media

### Preconditions
- A test object "2024.TEST.001 — Temporary Test Object" exists with two media rows attached.
- The object is not referenced by any loan.

### Steps
1. Open object "2024.TEST.001".
2. Click **Delete Object** and confirm.

### Expected Outcome
- The `objects` row is deleted.
- Both associated `media` rows are automatically deleted by `ON DELETE CASCADE` on `media.object_id`.
- No orphaned rows remain in `media`.
- `audit_log` records the DELETE of the object with a full row snapshot. Cascade-deleted `media` rows are **not** individually logged — they are recoverable from the object's snapshot (see schema_phase1.md audit_log notes).

### Schema Coverage
- `objects`: DELETE
- `media`: DELETE (via ON DELETE CASCADE)
- `audit_log`: INSERT
