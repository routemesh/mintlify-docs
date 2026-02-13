# RouteMesh Docs

This repository contains the source for RouteMesh documentation.

- Public docs URL: `https://routeme.sh/docs`
- Platform URL: `https://routeme.sh`
- Docs framework: Mintlify

## Local development

From this directory:

```bash
npm install
npm run dev
```

Then open `http://localhost:3000`.

## Build

```bash
npm run build
```

## Project structure

- `docs.json` - global docs configuration (navigation, branding, navbar/footer, API reference wiring)
- `intro/` - consumer-facing docs pages
- `provider/` - provider-facing docs pages
- `api-reference/openapi.json` - API reference schema used by the API tab
- `images/`, `logo/`, `snippets/` - shared docs assets/content

## Editing guidelines

- Keep language concrete and product-specific to RouteMesh.
- Prefer stable links under `https://routeme.sh/docs/...` in docs and app copy.
- When API behavior changes, update both:
  - prose docs in `intro/` or `provider/`
  - `api-reference/openapi.json`

## Docs/code sync

Use these files when validating docs against the codebase:

- `DOCS_CODE_MAP.md`
- `PROMPT_DOCS_SYNC.md`

## Deployment

Changes are deployed via Mintlify when updates land on the default branch of this repository.
