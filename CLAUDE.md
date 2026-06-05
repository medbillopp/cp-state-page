# Contracting Providers State Pages

## What This Project Does
Generates Elementor JSON templates for each **state coverage-area page** on contractingproviders.com (e.g. `/coverage-area/alabama`), using state-specific copy files. Each page markets Contracting Providers' Medicaid provider enrollment services for that state.

## Files
- `template.json` — Base Elementor JSON state page, minified onto a single line. Currently populated with the **generic national baseline** copy from `state-page.md` (H1 "Medicaid Enrollment Assistance for Healthcare Providers"), which serves as the working baseline. Contains the JSON-LD schema (Service, HowTo, LocalBusiness) embedded inside an HTML widget. Never modify this.
- `state-page.md` — Master copy reference and the **single source of truth** for keys. Shows which label maps to which widget and (for `SCHEMA_*` keys) which JSON-LD schema field. Header is `# State: [STATE]`.
- `state-page-schema.html` — Human-readable copy of the JSON-LD schema embedded in `template.json`, kept for reference.
- `copy/[state].md` — State-specific copy using the same keys as `state-page.md`.
- `output/[state].json` — Final Elementor JSON output, one per state.

## How to Run
When given a new state copy file (`copy/[state].md`), replace all baseline content in `template.json` with that state's values and save the result to `output/[state].json`.

## Rules
- Never modify `template.json`.
- Keep all JSON structure, IDs, and styles intact.
- Only replace text content values.
- **`state-page.md` is the single source of truth for keys.** The keys defined there are the contract between the copy files and the template. Only the value to the right of the `:` changes per state.
- **Use only the keys that exist in `state-page.md`.** A `copy/[state].md` file may contain extra keys or whole extra sections (for example a `METADATA` section, additional `SERVICE_ITEM_*`, extra comparison rows, etc.) that are **not** defined in `state-page.md` — **ignore them.** Do not add them to the template or the output. Only keys present in `state-page.md` are used.
- If a `copy/[state].md` file is **missing** a key that exists in `state-page.md`, stop and ask the user rather than guessing a value.
- Do not invent, rename, or remove keys in `state-page.md` itself without the user's go-ahead. To add a new key to the contract, update `state-page.md` first.
- Output must be valid, importable Elementor JSON.

## Implementation Notes
- Always use Python (not PowerShell) to read/write JSON files. PowerShell 5.1 writes UTF-8 with a BOM, which makes the file invalid for Elementor import.
- Text in the JSON often contains embedded HTML markup (e.g. `<strong>`, `<em>`) and escaped slashes (e.g. `<\/strong>`). Replacements must match the actual raw file content including these characters, not just the plain-text version from the copy files.
- `template.json` is minified onto a single line — match against the raw content accordingly.
- The JSON-LD schema is embedded inside an HTML widget in `template.json`; the `SCHEMA_*` keys map to fields inside it.
- After generating each output file, validate it parses as JSON before saving (`json.loads(content)`).
- Do not leave any helper or temporary scripts (e.g. `.py` files) in the project folder. Run logic in-memory and clean up after.
