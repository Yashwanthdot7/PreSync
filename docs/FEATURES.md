# PreSync feature reference

Detailed behaviour of each part of PreSync. For setup and an overview, see the [README](../README.md).

## Importing, editing and exporting decks

- **Import:** PowerPoint (`.pptx`) and PDF (`.pdf`), up to 25MB, from the dashboard. Word files never become slides: they open in the document editor (see *Word documents*). The file is analysed first (`POST /api/imports`) and the user reviews the result before anything is created:
  - *PowerPoint:* text boxes with position, size, rotation, insets and vertical anchoring; fonts, sizes and formatting per run, following the slide → layout → master inheritance; bullets and automatic numbering; tables (merged cells, fills, borders, built-in table styles); shapes (preset and custom geometry, fills, gradients, outlines); cropped pictures; colour, gradient and picture backgrounds; and speaker notes. Charts (column, bar, line, area, pie, doughnut, scatter; clustered or stacked) are read from their cached data and redrawn as chart blocks with their colours, title, legend and labels; 3-D and combo charts are drawn simplified and say so. SmartArt is redrawn as ordinary, editable shapes from the layout PowerPoint saves with it. Embedded objects (spreadsheets, Visio drawings…) show their preview picture. Anything still unreadable becomes a labelled placeholder and is listed in the review. *Open in editor* keeps the slides as extracted; *Generate from outline* rebuilds them as a fresh, consistently laid-out deck.
  - *PDF:* each page's drawing (pictures, shapes, charts) is rendered with PDF.js as the slide background, with the page's text on top as editable, aligned text boxes (fonts, sizes, bold/italic, colours, lists and the page title). Scanned pages without real text keep their full render. Rendering needs the optional `@napi-rs/canvas` package; without it PDFs import as text only.
  - Legacy `.doc` and `.ppt` are refused with a prompt to re-save them. Imports are kept on the **Documents** page, so the same file never has to be uploaded twice. Analysis is deterministic and uses no AI.
- **Edit:** the format bar above the slide changes with the selection.
  - *Text:* font menu (search, categories, favourites, recently used, each font drawn in its own face), size, bold/italic/underline/strikethrough, colour, highlight, alignment, line and paragraph spacing, bulleted and numbered lists with indent levels (Tab / Shift+Tab), superscript and subscript, clear formatting and a format painter. Formatting applies to the selected characters while typing, or to whole text boxes.
  - *Elements* (Insert › Elements): search 26 original stickers and decorations, 50 line icons you can recolour, and 119 emoji, by name or meaning ("growth", "security"), filtered by kind and category. Recently used elements and the pictures already in the deck are kept at the top. Stickers and icons are inserted as pictures, so they export to PDF and PPTX. Emoji are inserted as text in the viewer's own emoji font, so they look slightly different on each system.
  - *Smart guides:* a dragged element snaps to the slide's edges and centre and to other elements' edges and centres, and a guide line shows what it lined up with. Hold Alt to move freely, or turn snapping off under **More** (⋯). Snapping applies to moving, not resizing.
  - *Command palette* (Ctrl+K / ⌘K) runs any editor command by name: insert, layouts, themes, align, group, export, present and more. **Keyboard shortcuts** (Ctrl+/) lists every shortcut.
  - *Insert:* text styles (title, heading, body, list, quote, caption), 27 shapes including lines and arrows, tables (size picker), charts (with sample data), pictures (upload, drag onto the slide or paste), dividers, and new slides from a layout.
  - *Tables:* type into cells in the side panel (Tab moves on, Tab in the last cell adds a row), select ranges with Shift-click, insert and delete rows and columns, merge and split cells, cell fill, text colour, bold, horizontal and vertical alignment, padding, borders (all, outer, inner, none; colour and width), four table styles and a header-row switch. Paste a spreadsheet range into any cell.
  - *Charts:* change the type and grouping, edit the data sheet (or paste a range), series colours, title, legend, data labels and percent format.
  - *Pictures:* replace, crop each edge, flip, opacity, border, shadow, alt text, keep-proportions and reset to original proportions.
  - *Arrange:* exact position, size and rotation; align to the slide or to each other; distribute; group (Ctrl+G / Ctrl+Shift+G); lock; layer order; arrow keys nudge (Shift for 10px).
  - *Slides:* 12 layouts that move existing content into place without deleting anything, 8 themes (colours and fonts only, for one slide or all), background colour, rename and hide slides (hidden slides are skipped when presenting and left out of PDFs), reorder, duplicate and speaker notes.
  - Undo/redo (Ctrl+Z / Ctrl+Shift+Z) covers every edit, including layouts and themes, as single steps. Edits autosave through the live co-editing session; **Save** (Ctrl+S) writes them to the database immediately.
  - Fonts: the web fonts in the font menu are open-licence families (SIL Open Font License 1.1, Permanent Marker under Apache 2.0) loaded from Google Fonts the first time a slide uses them; PreSync does not bundle or redistribute them. Office font names use the device's copy or an open metric-compatible stand-in. The full list with each licence is in `frontend/src/data/fonts.js`. Layouts, themes, table styles, shapes, templates, stickers and icons are PreSync originals, and their licences are recorded in the source (`frontend/src/data/assetLibrary.js`).
- **Review and collaborate:**
  - *Comments* (speech-bubble button): comment on a slide, or on the selected element. Threads support replies, resolve and reopen, and only the author can edit their text; the author or the deck owner can delete a thread. Open threads show as pins on the slide. Everyone editing sees new comments within moments (`/api/decks/:id/comments`). Viewers of a live session never see comments.
  - *Version history* (**More › Version history**): save named versions, and Save also keeps an automatic version at most every 10 minutes when something changed. You can preview any version slide by slide. Restoring one replaces the slides for everyone, saves what was there first as a "Before restoring" version, and can be undone with Ctrl+Z. Each deck keeps up to 100 versions, and the oldest automatic ones are dropped first (`/api/decks/:id/versions`).
  - *Live selections:* while co-editing, each collaborator's selected element is outlined in their colour with their name, next to their cursor.
- **Templates:** *Templates* on the dashboard starts a presentation from one of six starter templates (startup pitch, lesson plan, project update, quarterly review, workshop, portfolio). They are PreSync originals built from its own layouts and themes, and they become ordinary, editable slides. **More › Save as template** in the editor saves the open presentation, with everyone's latest edits, as one of your own templates (`/api/templates`). Saved templates are private to you, up to 50 each, and listed under *My templates*.
- **Export:** *Export* in the editor downloads the deck as `.pdf`, `.pptx` or a Word outline `.docx` (`GET /api/decks/:id/export?format=pdf|pptx|docx`, with optional `hidden=1` to include hidden slides and `notes=1|0` for speaker notes), or the current slide as a PNG or JPEG image (drawn in the browser at twice the slide's size). PPTX keeps text editable with its per-run formatting (including highlight, superscript and subscript), plus tables, native (editable) charts, shapes, pictures (with crop, transparency, shadow and alt text), picture backgrounds, hidden slides and speaker notes (gradients export as their first colour; picture borders are not exported to PPTX). The Word outline is a document to read or edit rather than a copy of the slides: slide titles become headings, followed by the text with its formatting, tables, each chart's data as a table, pictures and speaker notes. Layout and shapes are not kept, and slides that only exist as a picture are included as that picture, labelled. *Export options…* shows what each format keeps and lets you include hidden slides and speaker notes; in PDF, notes become a notes page after each slide that has them. PDF draws the same content, including gradients, picture crops, transparency and borders, and leaves out hidden slides (picture shadows and text highlight are not drawn in PDF). It embeds Carlito, Caladea and Liberation fonts when the server has them installed (`fonts-crosextra-carlito`, `fonts-crosextra-caladea`, `fonts-liberation`; other font folders via `PDF_FONT_DIRS`), so text keeps the widths of Calibri, Cambria, Arial, Times New Roman and Courier New; otherwise it falls back to the standard PDF fonts, which cover Latin-1 text only.

## Word documents

Word documents are independent of presentations. `/documents` lists them; **New Word document** starts a blank one and **Import Word document** opens a `.docx` for editing at `/documents/:id`.

- **Editor:** built on TipTap. Fonts, sizes, line spacing, bold/italic/underline/strike, alignment and colour; title, headings, lists (with bullet and number styles, which export to .docx and PDF), blockquotes, captions, dividers and page breaks; tables (grid or exact size, merge, split, shading, column resize); pictures (PNG, JPEG, GIF up to 1.5MB, with resize and alignment); shapes, badges and callout cards. A4 or Letter. Changes save on their own a moment after each edit.
- **Pages:** the editor shows separate sheets, with text flowing onto the next one (`components/documents/pagination.js`); `Ctrl+Enter` inserts a page break. Double-click a top or bottom margin to edit the header and footer (left, centre and right text with title, date and page tokens, and a different first page). Insert > Page has page numbers, and the toolbar has the page border. All of this exports: `.docx` uses native header and footer parts, `PAGE`/`NUMPAGES` fields and `w:pgBorders`, except that Word cannot border chosen pages, so a specific-pages border exports on every page.
- **Import:** `POST /api/documents/import-docx` keeps headings, run formatting, lists, tables with merges and shading, pictures, page breaks, page size and margins; charts become tables of their data. Header and footer text comes in as the three slots. Text boxes, equations, footnotes, comments and SmartArt are not imported, and the editor says so when a file had any.
- **Export:** `GET /api/documents/:id/export/docx` and `/export/pdf`. Shapes survive export but not a later re-import. The PDF embeds an installed font when it can (Windows fonts, or Liberation/Carlito on Linux; `PDF_FONT_DIRS` adds folders) and otherwise uses the standard PDF fonts, which cover Latin text only.
- **Page numbers in Word:** Word recalculates its own pages, so a page can break in a slightly different place there than in the editor or PDF.
- **Sharing:** **Share** in the editor gives someone with a PreSync account *Can edit* or *Can view* access by email; the owner can change or remove it, and that person's open editor reloads with their new access. Documents shared with you are listed under *Shared with you* on the Documents page and on **Shared With Me**.
- **Co-editing:** the editor opens the document's live copy (Yjs over `/yjs/documents/:id`, `backend/src/yjs/documentManager.js`), so everyone with access sees each other's text, title, page size, header/footer and border changes as they happen, with each person's cursor and name in the text and who else is here in the header. The server writes the live copy to the database a moment after each change, when the last person leaves, and before a download. Undo only undoes your own changes. If the live connection can't be made, the editor says so and saves each change over the API instead.
- **View only:** people who can view follow the live document read-only, without the toolbar, and can download it and read comments. Their changes are never applied by the server.
- **Comments:** select text and open the comments panel to start a thread on it; the text is highlighted for everyone and clicking it opens the thread. Threads support replies, resolve and reopen; the author or the owner can delete one. Everyone with the document open sees comment changes within moments.
- **Version history:** save named versions; **Save** also keeps an automatic one at most every 10 minutes when something changed. Open any version to read it, and restore it for everyone; what was there first is kept as a "Before restoring" version. Up to 100 versions per document.
- **API:** `POST`/`GET /api/documents`, `GET`/`PUT`/`DELETE /api/documents/:id`, `POST /:id/save`, `POST /:id/share` and `PATCH`/`DELETE /:id/share/:userId`, `/:id/comments` (with `/replies`), `/:id/versions` (with `/restore`). Content is stored as the editor's JSON tree, validated by `backend/src/services/documentContent.js`, up to about 15MB per document including pictures.

## AI Copilot

*Copilot* in the editor's top bar opens an assistant beside the canvas (an overlay below 1400px). It works on what you are editing: the selected text or objects, the current slide, or the whole deck, shown and switchable above the message box.

- **Assist:** ask in your own words or pick a suggested prompt. It can rewrite, shorten, expand or fix the selected text, convert it to bullets or a table, suggest titles, write speaker notes, introductions and conclusions, summarise slides or the deck, recommend a layout, and suggest stickers, icons and emoji from PreSync's library. Each answer is labelled as coming from your presentation, general knowledge, or both, with links to the slides it used.
- **Proposed changes** arrive as cards with a preview (before and after text, before and after slide thumbnails, a table preview, or asset tiles). Nothing changes until you apply one; *Undo* on the card or Ctrl+Z reverts it. Changes go through the same collaborative operations as manual edits, so co-editors see them live, autosave and *Save* store them, and an action whose text was edited by someone else in the meantime is refused instead of overwriting it.
- **Create deck:** enter a topic, audience, purpose, slide count, tone and optional style. Copilot drafts an outline that you can edit, reorder and trim, then pick colours and where the slides go (after the current slide or at the end; an untouched blank deck can be replaced). Existing slides are never overwritten.
- **Check design** runs the editor's own checks on the current slide, without AI: text running off the slide or out of its box (from measured text heights), overlaps, edges a few pixels out of line, tiny type, too many fonts or words, and low contrast. Fixes that can be computed are offered as an *Apply fixes* card. The same report is sent with AI requests so design advice is based on real geometry.

**Privacy and safety.** The first time, Copilot explains which provider receives the content and asks to continue. Each request sends only what it needs: the active slide in detail, the selection, slide titles, and for deck-wide questions the slides most related to the request (others abridged). The server trims that again, keeps it inside a data block that the model is told never to take instructions from, and validates every proposed change against the editor's supported operations (`backend/src/ai/actions.js`), dropping anything that names blocks, layouts or assets that don't exist. The model never runs code or writes to the database; the browser applies approved changes. Conversations are saved per user and deck without slide content, are private to that user, are deleted with the deck, and can be cleared from the panel. Requests are rate limited per user, and neither prompts nor document text are logged.

**API** (signed-in editors only): `GET /api/ai/status`, `POST /api/ai/decks/:deckId/assist`, `GET|POST|DELETE /api/ai/decks/:deckId/conversations`, `GET|DELETE /api/ai/conversations/:id`, and `PATCH /api/ai/conversations/:id/messages/:messageId/actions/:index` to record whether a suggestion was applied.

**Code map.** Backend: `src/ai/` (config, provider adapter in `providers/`, context limits, prompt, structured response and action validation, rate limiter), `src/controllers/aiController.js`, `src/routes/aiRoutes.js`, `src/models/AiConversation.js`. Frontend: `src/components/copilot/` (panel, action cards, outline builder, editor bridge), `src/data/copilotContext.js` (what is sent), `src/data/copilotActions.js` (previews and edits on the Y.Doc), `src/data/layoutAnalysis.js` (design checks), `src/data/copilotLayouts.js` (layouts and colour themes) and `src/data/assetLibrary.js` (stickers, icons and emoji with licensing). Adding a provider means a new file in `backend/src/ai/providers/` exposing `generateJson()`.

**Limits.** Selection rewrites need the selection from the current session (a suggestion reloaded from history can't replace text blindly). Copilot doesn't see pictures' content, only their size and position, and it doesn't edit inside existing tables. *Undo* on a card reverts the most recent Copilot change.

## Design system

The UI follows the *Dark Forest Studio* theme: charcoal surfaces, forest green for primary actions and selection, crimson for live and destructive states. All colours, radii, shadows and fonts are CSS custom properties in `frontend/src/styles/theme.css`; shared primitives (dialog, confirm dialog, toast, menu, avatar, logo) live in `frontend/src/components/ui/`. Icons come from [`lucide-react`](https://lucide.dev).

Signed-in pages use the sidebar layout in `frontend/src/components/layout/AppShell.jsx`:

| Route | Page |
| --- | --- |
| `/decks` | Dashboard: recent and shared presentations, search, filter, sort |
| `/decks/mine` | Presentations you own |
| `/decks/shared` | Presentations shared with you |
| `/documents` | Imported files (`GET /api/imports`) |
| `/imports/:id` | Review an import: analysis, outline editor, document or slide preview |
| `/sessions` | Your live and recent sessions (`GET /api/rooms/mine`) |

The editor (`/editor/:deckId`) has its own full-screen layout:

- **Left:** slide thumbnails, with keyboard navigation and a per-slide menu for duplicate, move and delete.
- **Centre:** the Fabric canvas, with zoom and *Fit*.
- **Right:** a formatting panel that changes with what is selected.

Below 1100px the formatting panel opens as an overlay. Below 768px the slide list does too.

*Insert → Image* embeds PNG, JPEG or GIF files up to 1.5MB.

**Live sessions**

- *Present* in the editor saves the deck and opens the presenter view (`/presenter/:code`). It shows:
  - The current slide, next-slide preview and speaker notes.
  - Viewer count and connection state.
  - The audience Q&A queue.
  - *Invite*, with the code, a join link (`/join?code=…`) and a QR code.
- *End session* asks for confirmation first.
- The audience view (`/view/:code`) scales the slide to any screen and lets viewers catch up when the presenter moves on. It has private per-slide notes and Q&A.
- Both views show connecting, reconnecting and ended states. socket.io reconnects automatically.

