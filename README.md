# scan-with-ai

A Grist custom widget ("Upload with AI"): one file per row. Drop a file on
the selected row, and the widget stores it as the row's attachment, extracts
its text (PDF.js for PDFs, or the plain text) and, on "Scan with AI", asks
the AI gateway you configure for structured metadata (JSON) and a written
summary, saved to their columns. The gateway URL and key stay in your
browser's `localStorage`, never in the Grist document.

<!-- install-url:start -->
**Install URL**, to paste into a Grist custom widget's URL field: <https://arthurblanchon.github.io/scan-with-ai/latest/>
<!-- install-url:end -->

**Try it:** <https://arthurblanchon.github.io/scan-with-ai/latest/demo/>, Grist running in your browser with sample data (nothing is saved).

## In Grist

Add a custom widget, paste the install URL, and set its access level to
**Full document access** (to upload attachments and write the results).

Then map its columns:

| Column | Type | What it is |
| --- | --- | --- |
| `File` | Attachments | the row's file, required |
| `FileMetadata` | Text | AI metadata, as JSON |
| `FileExtract` | Text | the extracted text, not AI |
| `AiText` | Text | the AI-written text |

## Code

The widget's own code is in `src/`:

- `src/app/App.tsx` renders `src/components/widget-app.tsx`: the drop zone,
  the file view and the scan progress.
- `src/ai/` holds the gateway client, its settings and the scan (prompt,
  schema, progress); `src/lib/pdf-text.ts` extracts a PDF's text.
- `GRIST_OPTIONS` is in `src/grist-options.ts`.

## Develop

Develop on the `dev` branch; every push deploys `/dev/`
(<https://arthurblanchon.github.io/scan-with-ai/dev/>), which reloads itself in an
open Grist document a few seconds later.

| | |
| --- | --- |
| Install | `pnpm install` |
| Test (emulated Grist, no browser) | `pnpm test` |
| Lint, type-check, build | `pnpm lint`, `pnpm typecheck`, `pnpm build` |
| The widget in a real local Grist (Docker) | `pnpm demo` |
| Regenerate the demo page | `pnpm demo --save` |

`src/main.tsx` is one `mountGristWidget()` call from `grist-widget-sdk/app`:
inside Grist it renders the widget behind the SDK's connection and
column-mapping screens, with its alerts, in Grist's light or dark theme.
Opened anywhere else, it shows which build this is and the URL to paste into
Grist. The widget's code reads Grist only through `grist-widget-sdk`
([docs](https://gristwidgets.com/docs)), never the `grist` global.

`src/main.tsx`, the workflows and the build config are the template's: they
come from `create-grist-widget`, and `pnpm exec create-grist-widget upgrade`
(run on `dev` after Dependabot updates it) rewrites them
([Upgrading a widget](https://gristwidgets.com/docs/cli/upgrading)).
`AGENTS.md` says which files are which.

## Release

1. On `dev`, bump `package.json`'s `version`.
2. Open a pull request from `dev` into `main`, and merge it once its checks
   are green.

Merging builds `/v<version>/` and updates `/latest/` and their demo pages.
Without a version bump, nothing is published. The deploy workflow and its
one-time repository settings are described in the
[template's README](https://github.com/ArthurBlanchon/grist-widget-template#deployment).
