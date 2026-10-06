# Validation and known limits

## Static checks completed

- The sanitized export parses as JSON and retains 39 nodes.
- Connection sources and destinations resolve to known node names.
- Embedded token patterns and node credential references were removed.
- Workflow activation is off and pinned data is empty.
- Loop batch size is explicitly one; original connections are retained.
- Generated/reference image files were opened for a visual gallery check.

No live n8n execution, model-quality evaluation, provider quota test or Supabase write was performed. Setup-dependent behavior remains unverified.

## Issues visible in the source

1. **Sheet field propagation:** The planning JavaScript generates `Rendering_Mode` and label/asset metadata, while `Append row in sheet` explicitly maps only a smaller set of fields. Add the required mappings before expecting packaging branches to receive all context.
2. **Naming mismatch:** Update mappings include fields such as `Rendering Mode`, `Product Type` and `Selected Product Label Url`, while upstream objects use underscore names. Reconcile the actual column names and expressions in n8n.
3. **Filename/URL mismatch risk:** Image normalization names the binary from `Row_ID`, but `Generate Image URL` constructs its filename from `Day` and `Post_Number`. Verify the upload endpoint path and emitted public URL reference the same object.
4. **Per-item binary association:** Several restore nodes use `$item(0)` references. Check multi-row runs for stale/cross-row images. A one-row batch alone does not establish correct item pairing.
5. **Date validation:** The source computes duration using an absolute date difference. Validate reversed dates and invalid inputs before campaign generation.
6. **Retries and alerts:** Automated failure queues, notifications and robust retry/backoff handling are roadmap work, not implemented guarantees.
7. **Asset fidelity:** Logo, mascot and product-label preservation rely on generative editing. Human review is required; text distortion remains possible.
8. **External dependencies:** Exact n8n release, credential scopes and model access are not supplied. Baseline product/brand assets referenced by the workflow are not fully included in the source folder.

## Smoke-test checklist

Run one post, then three distinct posts, then both rendering branches. Check correct row IDs, matching filenames/URLs, image binaries, valid public outputs and completion statuses. Re-run only pending rows and inspect whether uploads/row updates produce duplicates. Simulate a failed image request or upload to establish recovery behavior before describing this as production ready.

## Security note

The original supplied workflow contained an embedded storage authorization token. It is excluded from this package. Rotate/revoke that token in the source deployment and update the original shared export. Removing it here does not revoke it.
