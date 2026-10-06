# Setup and execution

## Accounts and credentials

Use an n8n instance with the Google Sheets, Calendar and Gemini node types present in the export. The original export does not identify its n8n release, so exact version compatibility is unverified.

Attach OAuth credentials to each Sheets and Calendar node and an API credential to each Gemini node. Model names in the export are the original project configuration; choose models supported by your account when importing.

The `SUPAbase_Image_storage` HTTP Request node now uses Generic Credential Type → Custom Auth. Create a credential containing both the `apikey` and `Authorization` headers, with `Authorization` using the appropriate Bearer token. Put the real values only in n8n's credential store. The export preserves `Content-Type`. Use a credential permitted to write to the target bucket; do not place storage authorization tokens in workflow JSON.

## Deployment values

- Replace `YOUR_GOOGLE_SHEET_ID` in all five Sheets nodes.
- Select the correct `Sheet1` or `Asset_Catalog` tab in each node and refresh field mappings.
- Replace every `https://YOUR_PROJECT.supabase.co` occurrence, including JavaScript Code nodes.
- Check the India holidays Calendar selection against a calendar accessible to your account.
- Configure public access only for creative assets intended to be public; the workflow writes public image URLs.

The bucket name used by the source is `aquaponics-media`. Its reference paths include:

```
assets/products/
assets/labels/
assets/logos/
assets/mascots/
assets/designs/
```

The source includes fixed product filenames and product-to-label mappings in `Build Product Image List`, plus logo, mascot and design references in the brand/runtime catalog nodes. Upload matching files or adapt these mappings. The Drive folder contains final examples and documentation, not a complete reproducible baseline asset pack.

## Sheets

Import the header-only CSVs into tabs named `Sheet1` and `Asset_Catalog`. `Row_ID` is the content-row update key; `Status` is `pending` or `completed`; `Post_Type` controls whether a row enters rendering. Asset catalog rows need `Asset_Type=product` and `Status=active` for the brand-context filter.

The source has inconsistent underscore/spaced field names and incomplete append mappings. Inspect `docs/validation.md` before running; header files preserve the observed fields rather than silently rewriting business logic.

## Execution order

1. Run the asset initialization chain beginning at `Build Product Image List` as a selected-node/manual execution and confirm populated catalog rows. This chain has no separate trigger node in the supplied graph.
2. Submit campaign dates through `On form submission1`. Verify structured content rows appear in `Sheet1`.
3. Use the manual trigger for image rendering. Start with one pending Post and inspect raw/edited binary output, uploaded file and row update.
4. Test at least three distinct posts and both rendering branches before a full campaign. Confirm each row receives its own image and URL.

The loop batch size is one and the existing Wait node is set to three. Verify delay placement and its unit in your imported instance. Sequential processing helps manage request volume but does not guarantee compliance with provider quotas.
