[[+Home]] %% tags:: #MOC %% 

# Project MOC
Projects give the details and necessary information to understand a case. All of my projects are meetings stored here:  `06 - Main Notes/Projects`.

**Template:** [[Template, Project Note]]

```meta-bind-button
label: New project
icon: ""
style: default
class: ""
cssStyle: ""
backgroundImage: ""
tooltip: ""
id: ""
hidden: false
actions:
  - type: templaterCreateNote
    templateFile: 05 - Templates/Template, Project Note.md
    folderPath: 06 - Main Notes/Projects
    fileName: Project
    openNote: true
    openIfAlreadyExists: false
```

```dataviewjs
const projectsFolder = "06 - Main Notes/Projects"; // adjust if needed
const pages = dv.pages(`"${projectsFolder}"`)
  // Optionally: filter only files that explicitly declare type: project in frontmatter
  .where(p => (p.type && p.type.toLowerCase?.() === "project") || p.project_type);

function joinIfArray(x){ return Array.isArray(x) ? x.join(", ") : (x ?? ""); }

const rows = pages.map(p => {
  const status = joinIfArray(p.status);
  const priority = joinIfArray(p.priority);
  const start = p.start_date ?? "";
  const due = p.due_date ?? "";
  const tasksArray = (p.file?.tasks ?? p.tasks ?? []);
  const openCount = tasksArray.filter(t => !t.completed).length;
  return {
    link: p.file.link,
    status,
    priority,
    start,
    due,
    openCount,
    sortDue: String(p.due_date ?? "")
  };
});

// sort by due (ascending, blank last)
rows.sort((a,b) => {
  if(!a.sortDue && !b.sortDue) return a.link.toString().localeCompare(b.link.toString());
  if(!a.sortDue) return 1;
  if(!b.sortDue) return -1;
  return a.sortDue.localeCompare(b.sortDue);
});

dv.table(["Project","Status","Priority","Start", "Due","Open Tasks"],
  rows.map(r => [r.link, r.status, r.priority, r.start, r.due, r.openCount])
);
```
## Task Lists
### Project-Only Derived Tasks
```dataviewjs
const projPages = dv.pages(`"06 - Main Notes/Projects"`);

let projTasks = [];
projPages.forEach(p=>{
  (p.file?.tasks ?? p.tasks ?? []).filter(t=>!t.completed).forEach(t=>{
    projTasks.push([t.text, p.file.link, t.line ?? ""]);
  });
});

if (projTasks.length) {
  dv.table(["Task","Source Project","line"], projTasks);
} else {
  dv.paragraph("No open tasks inside project notes.");
}
```
### Meetings-Only Derived Tasks
```dataviewjs
const meetingsFolder = "06 - Main Notes/Meetings";
const meetings = dv.pages(`"${meetingsFolder}"`);

let meetingTasks = [];
meetings.forEach(p=>{
  const rpRaw = p.related_projects ?? [];
  const rp = Array.isArray(rpRaw) ? rpRaw : (rpRaw ? [rpRaw] : []);
  const tags = (p.file.tags ?? []);
  const projectNames = [...rp, ...tags].map(x => String(x));

  (p.file?.tasks ?? p.tasks ?? []).filter(t=>!t.completed).forEach(t=>{
    meetingTasks.push({
      text: t.text,
      source: p.file.link,
      projects: projectNames.length ? projectNames.join(", ") : "(no related_project)",
      line: t.line ?? ""
    });
  });
});

if (meetingTasks.length) {
  // Optionally: you can sort by projects or source file here
  dv.table(["Task","Meeting","Project(s)"], meetingTasks.map(x=>[x.text, x.source, x.projects]));
} else {
  dv.paragraph("No open tasks found in meeting notes.");
}
```