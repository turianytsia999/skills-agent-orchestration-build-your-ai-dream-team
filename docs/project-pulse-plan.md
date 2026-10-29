# Project Pulse implementation plan

## Summary

Build Mona’s Project Pulse as a lightweight static dashboard that helps contributors scan active projects, owners, statuses, recent activity, priorities or risks, and short summaries. The result will be a polished, accessible, responsive card-based UI served from `app/` through VS Code’s **Run Project Pulse Dashboard** launch configuration.

## File ownership and responsibilities

| Owner | Assigned files | Responsibility |
|---|---|---|
| Designer | `app/styles.css` | Define the visual direction: responsive dashboard layout, readable hierarchy, project-card appearance, status badges, priority/risk treatment, color contrast, spacing, focus states, and mobile behavior. |
| Coder | `app/index.html` | Create accessible dashboard markup, load `styles.css`, fetch `project-data.json`, and render one visible `.project-card` per project including name, owner, status, recentActivity, priority, and summary. Handle loading and data-fetch failure states explicitly. |
| Coder | `app/project-data.json` | Supply valid JSON with a top-level `projects` array. Every project must include `name`, `owner`, `status`, `recentActivity`, and `priority`; include contributor-friendly summary text used by the UI. |
| Coder | `.vscode/launch.json` | Create strict JSON without comments. Add **Run Project Pulse Dashboard**, run `python3 -m http.server 5500` with `cwd` set to `${workspaceFolder}/app`, and use `serverReadyAction` to open `http://localhost:%s/index.html`. |
| Orchestrator | Coordination and integration | Give Designer and Coder non-overlapping scopes, resolve interface decisions, run validation after integration, and report any remaining limitations. |

## Ordered implementation steps

1. **Confirm the data and rendering contract.**
   - Agree that `app/project-data.json` has a top-level `projects` array.
   - Require the fields `name`, `owner`, `status`, `recentActivity`, and `priority` for every item.
   - Add a short summary field so the dashboard meets the contributor-summary requirement.
   - Decide the supported status and priority labels before styling so the UI can present them consistently.

2. **Create the visual and accessibility direction.**
   - Designer defines the dashboard hierarchy: title and introductory context, project-card grid, project metadata ordering, badges, priority emphasis, and responsive breakpoints.
   - Designer specifies accessible color combinations, semantic emphasis beyond color alone, keyboard-visible focus treatment, and readable text sizing.
   - Designer owns only `app/styles.css` to avoid conflict with the Coder’s markup and behavior work.

3. **Implement deterministic project data and runnable preview support.**
   - Coder creates `app/project-data.json` with multiple realistic project records and all required fields.
   - Coder creates `.vscode/launch.json` as strict JSON, serving from `app/` on port 5500 and opening `index.html` rather than the server directory listing.

4. **Implement the dashboard page.**
   - Coder creates `app/index.html` with the exact title **Project Pulse**.
   - Link `styles.css`, reference/fetch `project-data.json`, and render data into visible `.project-card` elements.
   - Use semantic structure and accessible labels for project metadata.
   - Render status, recent activity, priority, owner, and summary for every project.
   - Provide clear loading, empty-data, malformed-data, and fetch-error messages instead of silently showing an empty page.

5. **Integrate and review the experience.**
   - Apply the Designer’s CSS to the Coder’s stable markup and CSS hooks, including `.dashboard` and `.project-card`.
   - Confirm cards, badges, priority cues, and mobile layout remain readable without relying only on color.
   - Resolve any mismatch between field names in the JSON and the rendering code.

6. **Validate the complete dashboard and hand off.**
   - Parse both JSON files, inspect static references, launch the configured preview, and verify the browser opens the dashboard at `index.html`.
   - Check the dashboard using desktop and narrow viewport widths, keyboard navigation, and representative valid and invalid data states.
   - Stop the preview server after validation and report validation results and limitations.

## Dependencies

- Step 1 is required before Steps 2–4 because the HTML renderer and CSS badges depend on stable field names and label values.
- `app/project-data.json` must be available before the final runtime behavior of `app/index.html` can be validated.
- The Designer’s visual decisions must be settled before final CSS integration, but initial markup and data work can begin from the agreed data contract.
- `.vscode/launch.json` must exist before end-to-end preview validation.
- Final integration and validation depend on all four deliverables being present.

## Parallel work

After Step 1, the following work can run in parallel because ownership does not overlap:

- **Designer:** implement the visual system in `app/styles.css`.
- **Coder:** create `app/project-data.json`, create `.vscode/launch.json`, and build `app/index.html`.

The Coder should preserve the agreed `.dashboard` and `.project-card` hooks so the Designer’s stylesheet integrates without markup changes.

## Sequential work

- Data contract decisions must precede implementation.
- CSS integration must follow stable HTML hooks and data-label decisions.
- End-to-end launch validation must follow creation of `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json`.
- Any fixes found during integration must be revalidated before handoff.

## Edge cases and risks

- A missing, malformed, or inaccessible `project-data.json` can leave the dashboard blank; show a concise error state.
- An empty `projects` array needs a useful empty state.
- Missing fields in an individual project should not break the full render; use a visible fallback such as “Not provided.”
- Unknown status or priority values need neutral, readable styling rather than assuming a predefined badge class exists.
- Color-only status or risk indicators can exclude some users; include text labels and sufficient contrast.
- Long project names, owner names, activity text, or summaries must wrap without breaking cards or horizontal layout.
- Fetching JSON from `file://` may fail due to browser restrictions; validation must use the configured local HTTP server.
- Incorrect `cwd`, URL, or server-ready configuration can open a directory listing instead of `index.html`.
- `.vscode/launch.json` must remain strict JSON with no comments.

## Validation expectations

- `app/project-data.json` parses as JSON and contains a top-level `projects` array.
- Every project record contains `name`, `owner`, `status`, `recentActivity`, and `priority`.
- `app/index.html` contains **Project Pulse**, loads `styles.css`, references `project-data.json`, and renders `.project-card` elements with status, recentActivity, and priority.
- `app/styles.css` contains `.dashboard` and `.project-card`, includes responsive layout behavior, `border-radius`, and `box-shadow`.
- `.vscode/launch.json` parses with `python3 -m json.tool .vscode/launch.json`.
- The **Run Project Pulse Dashboard** configuration uses `${workspaceFolder}/app`, runs `python3 -m http.server 5500`, and opens `http://localhost:%s/index.html`.
- Manual preview confirms the browser shows the Project Pulse dashboard rather than a directory listing.
- Manual accessibility review confirms logical heading order, visible focus indication, readable contrast, and a usable narrow viewport layout.
