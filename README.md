# Problem Review Sheet

A single-file, browser-based toolkit for ITIL **Problem Managers** running a weekly open-problem review. Drop in a ServiceNow export and get a grouped review sheet, branded images, formatted Excel workbooks, and ready-to-send resolver emails — with aging, staleness, Known Error callouts, and week-over-week trends computed for you.

**One HTML file. No install. No server. Nothing leaves your browser.**

![screenshot placeholder](docs/screenshot.png)

---

## Why

The weekly problem review usually means the same 45 minutes of manual work: export the list, prettify it in Excel, screenshot tables into emails, paste the Teams invite into every message, and try to remember which tickets moved since last week. This tool does all of that from one export file, and adds the questions leadership actually asks — *how old are these, which ones are stale, what changed since last week?*

## Features

### Data
- **CSV and JSON** input — ServiceNow list exports (`{"records":[…]}`), REST API responses (`{"result":[…]}`), or plain arrays. Display-value objects are flattened automatically.
- **Dynamic columns** — every column in your export appears in *Group by* and the *Columns* picker, including custom `u_*` fields.
- **Closed problems filtered automatically** — anything with a state containing "closed" is dropped on load, with a visible count.

### Computed insight
- **Age (days)** — from `sys_created_on`/`opened_at`, with color-coded buckets (0–30 / 31–60 / 61–90 / 90+). Tickets sort oldest-first within each priority.
- **Stale flag** — tickets with no update in *N* days (configurable, default 14). Uses `sys_updated_on` if exported; otherwise it finds the newest timestamp inside the **work notes text**; never-touched tickets fall back to created date.
- **Known Errors** — detected from a `known_error` flag or a "Known Error" state. Flagged rows get a **KE** badge, and emails can include a Known Errors section with workarounds.
- **Week-over-week delta** — each load is snapshotted locally; the next different-day load compares real ticket-number sets: `▲3 new · ▼5 cleared since Jul 4`, overall and per team. Re-loading the same day never destroys the comparison.

### Outputs
- **Excel (.xlsx)** — one styled workbook, or one per group (zipped). Priority, age, and stale cells are color-coded. Follows your theme.
- **Images (PNG)** — branded captures with your logo, title block, and theme accent — for pasting inline anywhere.
- **Ready-to-send emails (.eml)** — one per resolver group, each with:
  - the team's problem table inline as an image,
  - their own .xlsx **plus** the all-teams master .xlsx attached,
  - pre-filled **To:** addresses (from an optional group→email CSV),
  - your weekly notes, Teams meeting details, and
  - an optional recurring **.ics calendar invite**.
  Plus a generic invite for stakeholders who don't own problems.
- **Copy for Outlook** — the generic invite as rich text on your clipboard.

### Communication, written once
- **Editable email templates** (resolver + generic) with live placeholders — see [Template tokens](#template-tokens).
- **Weekly notes** — typed once, inserted into every email.
- **Teams meeting details** — pasted once, appended to every email (links become clickable) and used as the .ics description.
- Empty sections and bullets disappear automatically — no dangling "•" when there's nothing to say.

### Appearance
- **Theme color + saturation sliders** and nine presets (seasonal reds/greens/oranges included) — the theme flows through the app, the images, the emails, and Excel.
- **Company logo** — shown in the app, at the top of every email, and integrated into the image headers.
- **Dark mode** for the app chrome, **Darken sheet** for the on-screen table (exports stay light), and **Darken exports** if you want the photos and Excel dark too. All independent.

### Settings that travel
- Everything auto-saves in your browser (localStorage).
- **Export/Import settings (.ini)** — a portable, hand-editable file carrying theme, logo, photo width, stale threshold, templates, notes, Teams details, and the meeting schedule. Ship a company-branded `.ini` next to the HTML and colleagues import your whole setup in one click.

---

## Quick start

1. Download `problem-review-sheet.html` and open it in any modern browser (Chrome, Edge, Firefox).
2. In ServiceNow, open your **Problems** list, filter as desired, right-click a column header → **Export → CSV** (or JSON).
3. Drop the file onto the page. Closed problems are filtered out automatically.
4. Pick your *Group by* field and columns.
5. Export what you need — or click **Email text** to set up templates, notes, Teams details, and generate the per-resolver `.eml` zip.

No file handy? Click **Load sample data** to explore.

### Pre-filling resolver addresses

In the email dialog, load a CSV with two columns — an assignment-group column and an email column (headers like `Assignment group` and `Email`; multiple rows per group are joined with `;`). Each generated `.eml` gets its **To:** field pre-filled.

---

## Template tokens

Templates drive every generated email and the copy buttons. Edit them in the email dialog; they save automatically.

| Token | Expands to |
|---|---|
| `{team}` | Assignment group name (resolver emails) |
| `{count}` | That team's open ticket count |
| `{total}` | Open tickets across all teams |
| `{groups}` | Bulleted per-group breakdown, with per-team deltas |
| `{groupField}` | The current *Group by* field name |
| `{date}` | Today's date |
| `{delta}` | Week-over-week: `(▲3 new · ▼5 cleared since Jul 4)` |
| `{teamdelta}` | Same, scoped to the team |
| `{stale}` | "*N tickets have had no update in 14+ days.*" |
| `{teamstale}` | Same, per team, with a nudge to update |
| `{knownerrors}` | "Known Errors with workarounds:" list |

Lines starting with `•` render as bullets. Bullets whose tokens expand to nothing are removed.

---

## Privacy

Everything runs client-side. Your ticket data is parsed in the browser and never uploaded anywhere. The only network requests are CDN loads for the bundled libraries (PapaParse, ExcelJS, html2canvas, JSZip) and Google Fonts. Snapshots, settings, and templates live in your browser's localStorage; the `.ini` export is a plain text file you control.

## Notes & limitations

- **.eml files** open as editable drafts in Outlook and Thunderbird — review, then send.
- **Week-over-week** data is per-browser (it's your local history, deliberately not part of the `.ini`). First run shows no delta; it lights up from the second week.
- Per-team deltas only display when you're grouping by the same field the baseline was taken with (the file's assignment-group column).
- The **stale** and **age** columns require a date column in your export — include `sys_created_on`, and `sys_updated_on` or work notes for best results.
- The `.ics` uses floating local time — fine within one company/timezone.
- Internet access is needed on first load for the CDN libraries.

## Tech

Vanilla JS in one HTML file. [PapaParse](https://www.papaparse.com/) (CSV), [ExcelJS](https://github.com/exceljs/exceljs) (real styled .xlsx), [html2canvas](https://html2canvas.hertzen.com/) (PNG capture), [JSZip](https://stuk.github.io/jszip/) (bundles). Emails are built as RFC 822 `.eml` with inline CID images and base64 attachments — no mail server involved.

## Contributing

Issues and PRs welcome. It's one file — open it, edit it, refresh.

## License

MIT (or your preferred license — update this section before publishing).
