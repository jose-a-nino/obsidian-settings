[[+Home]] %% tags:: #MOC %% 

# Monthly MOC
My daily notes are stored here: `06 - Main Notes/Daily Note` which is the basis of these month-year tables: to see all daily notes but grouped by month instead. 

```dataviewjs
// Group daily notes by YYYY-MM extracted from filename and render a table per month
const pages = dv.pages('"06 - Main Notes/Daily Note"')
  .where(p => !(p.file.tags || []).includes("MOC"))
  .where(p => p.file.name && p.file.name.length >= 7); // ensure name exists

// Helper to get YYYY-MM
function ymFromFileName(name) {
  return name.slice(0,7); // "YYYY-MM"
}

// Group pages by YYYY-MM
const groups = {};
for (let p of pages) {
  const ym = ymFromFileName(p.file.name);
  if (!groups[ym]) groups[ym] = [];
  groups[ym].push(p);
}

// Sort group keys descending (newest month first)
const sortedKeys = Object.keys(groups).sort().reverse();

// Helper to convert "YYYY-MM" to pretty month name, e.g., "January 2026"
function prettyMonth(ym) {
  const [y, m] = ym.split('-').map(Number);
  const d = new Date(y, m - 1, 1);
  return d.toLocaleString('default', { month: 'long', year: 'numeric' });
}

for (const key of sortedKeys) {
  const rows = groups[key]
    .sort((a,b) => a.file.name.localeCompare(b.file.name)) // sort within month
    .map(p => [p.file.name, p.file.cday, p.summary || "", p.related_projects || ""]);
  dv.header(3, prettyMonth(key)); // pretty month header like "January 2026"
  dv.table(["Date","Created","Summary","Projects"], rows);
}
```