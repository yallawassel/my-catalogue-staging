# My Catalogue — Universal POS synchronization

## Agreed product behavior

- Support customers using different POS brands.
- Support Excel/CSV uploads now and POS API/feed connectors later.
- Support **Price-only** and **Full product** synchronization.
- POS prices become the online catalogue prices automatically after confirmation.
- Optional percentage adjustment supports a markup or discount. `0` mirrors POS exactly; `10` adds 10%; `-5` discounts by 5%.
- Existing photos are preserved unless a replacement photo is explicitly selected in full-sync mode.
- No product is deleted because it is missing from an uploaded file.

## Matching order

1. POS Product ID (`pos_product_id`)
2. Barcode
3. SKU
4. Otherwise treated as a new product in Full product mode only.

Product name is never used as an automatic update key. Multiple matches are flagged for manual resolution.

## First-time setup

1. Back up the Supabase project/database.
2. Open Supabase → SQL Editor.
3. Run `POS_SYNC_MIGRATION.sql` once.
4. Deploy the updated `admin.html` and existing project files together.
5. Sign in as a business owner and open **Product & Price Synchronization**.
6. Download the POS template or upload an Excel/CSV exported from the POS.
7. Select Price-only or Full product sync, enter the desired online markup/discount, analyze, review and authorize.

## Supported spreadsheet columns

Recognized headings include:

- `POS Product ID`, `POS ID`, `Product ID`, `External ID`
- `Product Name`, `Name`, `Product`, `Item`
- `Barcode`, `EAN`, `UPC`
- `SKU`, `Item SKU`
- `Category`
- `Label1`, `Unit1`, `First Unit`; `Price1`, `Price 1`, `Selling Price`, `Wholesale Price`
- `Label2`, `Unit2`, `Second Unit`; `Price2`, `Price 2`, `Retail Price`
- `Description`, `Details`, `Specifications`
- `Photo URL`, `Image URL`, `Display Order`

Price-only mode changes only supplied non-empty price cells and `updated_at`. It does not change names, units, photos, descriptions or categories. Full mode can add new products and update matched product details.

## Safety and current scope

- Synchronization runs within the signed-in business scope and relies on the existing `mc_saas_can_manage(business_id)` RLS helper.
- Each job and per-product change is recorded in `mc_saas_sync_jobs` and `mc_saas_sync_changes`.
- Ambiguous identifiers are not auto-updated.
- This release implements file-based synchronization. Automatic API connections require POS vendor/API details, server-side credentials, and a scheduled Edge Function; no POS API connection is claimed to be active yet.
- Change history is audit data in this release; automated one-click rollback is a later enhancement.
