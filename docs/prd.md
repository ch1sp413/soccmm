# SOC-CMM Assessment Web App PRD

## 1. Summary

Build a browser-hosted web application that helps Security Operations leaders carry out routine SOC-CMM capability and maturity assessments without relying on the original spreadsheet workflow.

The app will provide a polished operational assessment experience, editable SOC-CMM model content, scoped assessments, historical tracking, full report browsing, and a lightweight improvement backlog. Version 1 is deployable by any organisation for its own internal use, but is not intended to be productised SaaS.

## 2. Goals

- Enable a SecOps leader to complete a SOC-CMM assessment without using the spreadsheet.
- Preserve the SOC-CMM scoring model as the default assessment logic.
- Support customisation of SOC-CMM model content without code changes.
- Support assessment-specific scope customisation across domains, aspects, and questions.
- Track completed assessments over time to show maturity, capability, target, and backlog progress.
- Provide full internal report browsing through the app.
- Keep the interface polished, dense, calm, and credible for security operations leadership.
- Use a deployable browser-hosted architecture from the start.

## 3. Non-Goals

- Productised SaaS, billing, public signup, tenant administration, or cross-organisation hosting.
- Multi-user assessment collaboration.
- Real-time workshop editing by multiple participants.
- Full GRC platform features.
- Evidence file management.
- Benchmarking against peer organisations.
- AI-generated improvement plans.
- User-facing Excel workbook import.
- Scope templates or predefined quick-scan presets in v1.
- Editable scoring formulas in the v1 UI.
- Compliance-grade audit logging.

## 4. Users and Deployment Model

### Primary User

The primary v1 user is a single SecOps leader or assessment owner per deployment.

The owner can:

- configure the app after deployment;
- create and manage the owner account;
- maintain the SOC profile;
- customise model content;
- create, scope, complete, revise, and archive assessments;
- manage improvement backlog items;
- import and export owner-only structured data.

### Viewers

Unauthenticated internal viewers can browse completed assessment reports, full completed assessment detail, historical trends, and the visible improvement backlog.

Viewers cannot perform write actions and cannot access drafts, active assessment workspaces, model editing, settings, import, export, or owner administration.

### Deployment Assumption

Any organisation may deploy its own instance. Each v1 deployment is assumed to be protected by organisational network controls. V1 is not safe to expose directly to the public internet unless the deploying organisation accepts that completed assessment details and improvement backlog data are readable without authentication.

## 5. Access and Security Assumptions

V1 intentionally uses an internal-trust model.

- Completed reports are browseable without authentication.
- Full completed assessment detail is browseable without authentication.
- Historical trends are browseable without authentication.
- Improvement backlog details are browseable without authentication, including title, linked domain/aspect/question, priority, status, owner, due date, and notes.
- Unauthenticated users have no app-provided export or download controls.
- Unauthenticated pages remain ordinary browser pages, so users may still copy, screenshot, or use browser-native printing.
- Owner authentication protects all write actions and administration.
- The UI and deployment documentation must warn that completed assessment results and remediation plans are internally visible.

Owner-only routes and actions include:

- create, edit, delete, complete, reopen, revise, and archive assessments;
- answer assessment questions;
- edit scope, weights, targets, remarks, evidence references, and rationale;
- manage model versions and model content;
- manage improvement backlog items;
- edit profile/settings;
- import/export model JSON;
- export assessment JSON/CSV and backlog CSV;
- seed/reset baseline data where supported.

## 6. First-Run Setup

V1 must include a first-run setup flow:

1. Create the owner account.
2. Confirm the internal visibility and deployment risk warning.
3. Seed the bundled SOC-CMM baseline model.
4. Create the default SOC profile.
5. Optionally set default target levels.
6. Land the owner on the dashboard with a clear "New assessment" action.

If setup has not run, unauthenticated users should be routed to setup rather than the report index.

## 7. Routing

- `/`: unauthenticated completed report index after setup.
- `/login`: owner login.
- `/app`: authenticated owner dashboard.
- `/setup`: first-run setup when no owner exists.
- `/reports`: unauthenticated completed report index.
- `/reports/:id`: unauthenticated full completed assessment report.
- Editing, model, settings, import, export, and backlog management routes require owner authentication.

If no completed reports exist, the unauthenticated report index shows an empty state with an internal visibility notice.

## 8. SOC Profile

V1 supports one reusable default SOC profile, not multiple SOC profiles or organisations.

The default profile should include fields such as:

- organisation name;
- SOC name;
- departments;
- operating scope;
- assessment purpose defaults;
- default target levels;
- relevant notes.

Each new assessment starts from the default profile. Completed assessments snapshot the profile values at completion, so later profile edits do not change historical reports.

## 9. Model Content

### Baseline Model

The official SOC-CMM workbook is a source for seed content, but the app must own an app-native validated model schema.

V1 includes:

- bundled SOC-CMM baseline JSON;
- a maintainer/developer conversion script may exist to regenerate baseline JSON from the workbook;
- no user-facing Excel import.

### Model Versioning

Every usable model is a versioned copy.

- The bundled baseline is preserved.
- Owners can duplicate the baseline to create a custom model.
- Editing custom model content creates or updates a model version according to the app's versioning rules.
- Completed assessments always reference and snapshot the exact model content used.
- Upstream SOC-CMM updates are manual/import-based through app-native JSON, not automatic sync.

### Stable IDs

Stable IDs are mandatory for:

- domains;
- aspects;
- questions;
- answer options;
- guidance items;
- mappings.

Editing text must not create a new ID. Replacing or removing a concept should archive it rather than silently reusing its ID for different meaning. Duplicated model versions retain IDs where the concept is the same.

### In-App Model Editor

V1 must include an in-app model editor for:

- domains;
- aspects;
- questions;
- maturity/capability applicability;
- answer options and labels where they map to existing scoring scales;
- question-level help;
- answer-level guidance;
- default weights;
- NIST CSF mappings where seeded.

V1 does not expose scoring formula, aggregation rule, score range, or scoring matrix editing in the UI.

### Portable Format

The canonical portable model format is JSON.

Model JSON must be:

- validated on import;
- versioned;
- stable-ID preserving;
- suitable for Git versioning and review.

## 10. Scoring

V1 preserves SOC-CMM scoring by default:

- maturity scoring from `0` to `5`;
- capability scoring from `0` to `3`;
- capability applies to Technology and Services by default unless model configuration says otherwise;
- optional importance/weighting from the advanced workbook model;
- domain and aspect aggregation;
- target maturity/capability levels.

The schema may allow future scoring configuration, but v1 UI must not allow arbitrary formula editing.

Answers are selected from discrete options only. V1 does not allow arbitrary numeric score overrides.

## 11. Guidance and Response States

V1 supports:

- question prompt;
- question-level help text;
- answer options;
- answer-level guidance where available;
- remarks/rationale per question response;
- evidence reference per question response.

Response states must distinguish:

- `Not assessed yet`: draft item missing a required response;
- `Unknown`: in scope but uncertain, handled according to model rules;
- `Not applicable`: explicitly excluded from scoring and requiring rationale;
- model-defined answer options such as "Not required" where they are scoring choices.

## 12. Assessment Scope

V1 scope customisation supports:

- include/exclude domains;
- include/exclude aspects;
- mark individual questions as not applicable;
- rationale required for excluded aspects/questions;
- assessment-specific targets by domain/aspect;
- assessment-specific weight overrides.

Reports and trends must show scope coverage. Trend comparisons must warn when assessments differ by scope, weighting, or assessment type.

Reusable scope templates are out of scope for v1.

## 13. Assessment Lifecycle

Assessments are point-in-time snapshots with draft support.

Lifecycle states:

- `Draft`: editable assessment in progress.
- `Completed`: locked snapshot used for reports, trends, and comparisons.
- `Revised draft`: created from a completed assessment when changes are needed.
- `Archived`: hidden from default views while retained for historical integrity.

Completed assessments are immutable through normal workflows. Edits after completion require creating a revised draft.

Drafts can be deleted. Completed assessments can be archived but not hard-deleted through normal UI.

Model versions used by completed assessments cannot be deleted. Unused custom model versions can be archived.

## 14. Assessment Types

V1 supports:

- `Full assessment`;
- `Quick scan`.

The assessment type is metadata plus scope defaults, not separate scoring logic.

Full assessments default to all applicable model content. Quick scans start from the model and rely on owner scope reduction in v1. V1 does not ship predefined quick-scan question sets unless separately defined later.

Reports and trends must clearly show assessment type and warn when comparing quick scans with full assessments.

## 15. Assessment Interface

The assessment UI should be a dense guided workspace, not a linear page-per-question wizard.

Required experience:

- left navigation for domains/aspects with completion status;
- central question table or panel for efficient answering;
- right contextual panel for guidance, scoring meaning, notes, evidence references, and scope rationale;
- keyboard-friendly answer entry;
- autosave drafts;
- visible progress and unanswered required items;
- filters for unanswered, flagged, excluded, or low-scoring items.

The UI should feel like a modern operational dashboard: polished, focused, fast, and readable.

## 16. Reporting and Trends

Unauthenticated completed reports include full detail:

- assessment profile snapshot;
- model name/version;
- assessment date and type;
- scope;
- included and excluded domains/aspects/questions;
- exclusion rationale;
- maturity scores;
- capability scores where applicable;
- target vs current by domain/aspect;
- weights and weight overrides;
- selected answers;
- remarks/rationale;
- evidence references;
- guidance text relevant at assessment time;
- NIST CSF mapping metadata where available;
- strengths and weakest aspects;
- linked improvement backlog items.

Trend views include:

- completed assessment history;
- current vs prior comparison;
- domain/aspect trend lines;
- target vs actual history;
- scope/weight/type comparability warnings;
- backlog status/progress over time.

Reports must render from completed assessment snapshots, not the current model.

V1 uses print-friendly report pages instead of built-in PDF generation.

## 17. Improvement Backlog

V1 includes a lightweight improvement backlog, visible in reports and owner-manageable in the app.

Backlog items can be created from weak aspects/questions and include:

- title;
- linked assessment;
- linked domain/aspect/question where applicable;
- priority;
- owner;
- status;
- due date;
- notes.

Improvement items live independently from the assessment that created them while retaining links to originating findings. Future assessments can show whether related scores improved while items were open or closed.

Backlog is visible without authentication as part of the internal report surface. Backlog editing remains owner-only.

## 18. NIST CSF Alignment

V1 includes NIST CSF alignment as inherited mapping/reporting metadata, not as a primary workflow.

V1 should:

- seed mappings from the workbook where available;
- preserve mappings when duplicating/editing model content;
- show NIST CSF coverage in reports;
- allow mapping edits in the model editor if practical;
- avoid becoming a full NIST CSF assessment tool.

## 19. Owner-Only Import and Export

Owner-only import/export capabilities:

- model JSON export;
- model JSON import;
- completed assessment JSON export;
- assessment history CSV export;
- improvement backlog CSV export.

Database backups are deployment/operator responsibility, not an app-managed v1 feature.

Unauthenticated users do not receive app-provided export/download controls.

## 20. Audit History

V1 includes lightweight lifecycle audit events for:

- first-run setup completed;
- assessment created;
- assessment completed;
- assessment reopened/revised;
- assessment archived;
- model version created/duplicated;
- model version archived;
- model version used for assessment;
- import/export actions.

V1 does not require keystroke-level, answer-change-level, or compliance-grade audit logging.

## 21. Visual and UX Requirements

The app should feel like a polished operational tool for SecOps leaders.

Design direction:

- dense but calm interface for repeated assessment work;
- strong information hierarchy;
- clear progress, scope, scores, targets, and trend visuals;
- spreadsheet replacement without looking like a spreadsheet;
- professional typography and high contrast;
- readable radar and comparison charts;
- no marketing landing page;
- no oversized hero sections, decorative card-heavy layouts, or gimmicky animation.

Cards should be used for repeated items or genuinely framed tools, not nested page sections.

## 22. Accessibility and Responsiveness

V1 is desktop-first.

Requirements:

- desktop assessment authoring is fully supported;
- tablet use is acceptable;
- mobile report browsing is supported;
- mobile assessment authoring may be limited;
- keyboard navigation for assessment entry;
- sufficient colour contrast;
- charts have table equivalents or accessible summaries;
- print-friendly reports remain readable without interactive affordances.

## 23. Implementation Stack

V1 implementation stack:

- Framework: Next.js App Router + TypeScript.
- Database: Postgres.
- ORM/migrations: Drizzle ORM.
- Validation: Zod.
- UI: Tailwind CSS + shadcn/ui/Radix primitives.
- Charts: Apache ECharts.
- Authentication: Auth.js/NextAuth credentials provider for the single owner account.
- Testing: Vitest, React Testing Library, and Playwright.
- Deployment: Docker Compose first-class, including Postgres service and seeded baseline model.

## 24. Data and Backup Responsibilities

V1 stores app data in Postgres.

Deployment operators are responsible for:

- database backups;
- restore procedures;
- network exposure controls;
- TLS/reverse proxy setup where needed;
- environment secrets.

The app provides owner-level JSON/CSV exports for portability, not scheduled backups or restore UI.

Archive is a product state, not a backup mechanism.

## 25. Testing Requirements

Automated tests must cover the core trust boundaries:

- model schema validation/import/export;
- seeded baseline model loads successfully;
- scoring and aggregation;
- assessment lifecycle and snapshot immutability;
- scope exclusion and not-applicable handling;
- authenticated owner-only writes;
- unauthenticated read-only report access;
- absence of unauthenticated export/download controls;
- archive behavior;
- trend comparability warnings;
- first-run setup.

## 26. Success Metrics

V1 is successful when:

- the owner can complete a full SOC-CMM assessment without using the spreadsheet;
- the owner can customise model content without code changes;
- the owner can customise assessment scope down to question-level applicability;
- completed assessment reports are understandable to internal stakeholders;
- unauthenticated internal viewers can browse full completed report detail, trends, and backlog without write access;
- trends make improvement or regression visible across assessment cycles;
- scoring is explainable and traceable to the completed snapshot;
- the baseline model seed matches the source workbook closely enough to trust;
- core flows pass automated tests.

## 27. Open Questions

- What exact fields should the default SOC profile include?
- What is the first baseline model JSON schema?
- How closely must the first seed match the spreadsheet before v1 is acceptable?
- Which chart types are required in the first reporting release beyond radar, bar, and trend line charts?
- Should archived completed assessments remain visible to unauthenticated viewers through direct URLs, or only to the owner?
- What is the minimum acceptable deployment documentation for internal network protection?
