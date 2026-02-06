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

```dataview
TABLE
  status AS Status,
  priority AS Priority,
  start_date AS Start,
  due_date AS Due,
  length(filter(file.tasks, (t) => !t.completed)) AS "Open Tasks"
FROM "06 - Main Notes/Projects"
WHERE type = "project" OR project_type
SORT due_date ASC
```
## Task Lists
### Open Tasks
```dataviewjs
// === CONFIG ===
const projectsFolder = "06 - Main Notes/Projects";
const meetingsFolder = "06 - Main Notes/Meetings";
const dailyFolder    = "06 - Main Notes/Daily Note"; // make sure spelling matches your vault
  
// If you only want active projects, set this to true and customize statuses below.
const onlyActiveProjects = false;
const completedStatuses = ["completed", "done", "closed"]; // adjust to your exact status values

// === HELPERS ===
const asArray = (x) => Array.isArray(x) ? x : (x ? [x] : []);
const isProject = (p) =>
  (p.type && String(p.type).toLowerCase() === "project") || p.project_type;
const isActiveProject = (p) => {
  const raw = asArray(p.status).map(s => String(s).toLowerCase());
  return raw.length === 0 ? true : !raw.some(s => completedStatuses.includes(s));
};
const openTasks = (page) => asArray(page.file?.tasks ?? page.tasks).filter(t => !t.completed);

// === PAGES ===
const projects = dv.pages(`"${projectsFolder}"`)
  .where(p => isProject(p))
  .where(p => !onlyActiveProjects || isActiveProject(p))
  .sort(p => p.due_date ?? dv.date("9999-12-31"), "asc"); // due-date sort, blanks last
const meetings = dv.pages(`"${meetingsFolder}"`);
const dailies  = dv.pages(`"${dailyFolder}"`);

// === RENDER ===
let total = 0;
for (const proj of projects) {
  const projLink = proj.file.link;
  // 1) tasks inside the project note itself
  const fromProject = openTasks(proj);
  // 2) tasks inside meeting notes linked to this project
  const fromMeetings = meetings
    .where(m => asArray(m.related_projects).some(rp => String(rp) === String(projLink)))
    .flatMap(m => openTasks(m));
  // 3) tasks inside daily notes linked to this project
  const fromDailies = dailies
    .where(d => asArray(d.related_projects).some(rp => String(rp) === String(projLink)))
    .flatMap(d => openTasks(d));
  const tasks = [...fromProject, ...fromMeetings, ...fromDailies];

  if (tasks.length) {
    total += tasks.length;
    dv.header(3, projLink);        // "### [[Project Alpha]]"
    dv.taskList(tasks, false);     // show tasks (unchecked/checked not forced)
  }
}
if (!total) dv.paragraph("No open tasks found across projects, meetings, or daily notes.");
```
### Completed Tasks
```dataviewjs
// === CONFIG ===

const projectsFolder = "06 - Main Notes/Projects";
const meetingsFolder = "06 - Main Notes/Meetings";
const dailyFolder    = "06 - Main Notes/Daily Note";


// Optional: keep the completed section from getting huge

const limitCompletedPerProject = 200; // set null to show all

const onlyActiveProjects = false; // usually FALSE for completed tasks; set TRUE if you want only active projects
const completedStatuses = ["completed", "done", "closed"];
  
// === HELPERS ===

const asArray = (x) => Array.isArray(x) ? x : (x ? [x] : []);
const isProject = (p) =>
  (p.type && String(p.type).toLowerCase() === "project") || p.project_type;

const isActiveProject = (p) => {
  const raw = asArray(p.status).map(s => String(s).toLowerCase());
  return raw.length === 0 ? true : !raw.some(s => completedStatuses.includes(s));
};

const doneTasks = (page) => asArray(page.file?.tasks ?? page.tasks).filter(t => t.completed);


// === PAGES ===

const projects = dv.pages(`"${projectsFolder}"`)
  .where(p => isProject(p))
  .where(p => !onlyActiveProjects || isActiveProject(p))
  .sort(p => p.due_date ?? dv.date("9999-12-31"), "asc");

const meetings = dv.pages(`"${meetingsFolder}"`);
const dailies  = dv.pages(`"${dailyFolder}"`);

// === RENDER ===

let total = 0;


for (const proj of projects) {
  const projLink = proj.file.link;  

  const fromProject = doneTasks(proj);

  const fromMeetings = meetings
    .where(m => asArray(m.related_projects).some(rp => String(rp) === String(projLink)))
    .flatMap(m => doneTasks(m));

  const fromDailies = dailies
    .where(d => asArray(d.related_projects).some(rp => String(rp) === String(projLink)))
    .flatMap(d => doneTasks(d));

  let tasks = [...fromProject, ...fromMeetings, ...fromDailies];

  if (limitCompletedPerProject) tasks = tasks.slice(0, limitCompletedPerProject);

  if (tasks.length) {
    total += tasks.length;
    dv.header(3, projLink);
    dv.taskList(tasks, false);
  }
}

if (!total) dv.paragraph("No completed tasks found across projects, meetings, or daily notes.");
```