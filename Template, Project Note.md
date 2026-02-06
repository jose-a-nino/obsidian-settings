---
project_type:
  - Work Project
type: project
start_date: "{{date:YYYY.MM.DD}}"
due_date:
status:
  - "{{Not Started / In Progress / Completed}}"
priority:
  - "{{High / Medium / Low}}"
team:
  - "{{Client / Team}}"
tags:
  - "#work"
  - "#project"
related_projects:
  - "{{Related Project 1}}"
  - "{{Related Project 2}}"
summary:
---
tags: [[Project MOC]]
## Overview
- **Start Date**: `= this.start_date`
- **Due Date**: `= this.due_date`
- **Status**: `= this.status`
- **Priority**: `= this.priority`

## Objective
- **Goal**: {{Brief description of what this project aims to achieve}}
- **Client / Team**: `= this.team`

## Steps / Tasks

### General Tasks
- [ ] {{Task 1 description and any sub-tasks or details needed}}
- [ ] {{Task 2 description and any sub-tasks or details needed}}
- [ ] {{Task 3 description and any sub-tasks or details needed}}
### Tasks from Meetings
```dataviewjs
// Collect open tasks from meeting notes that match this project's related_projects
const meetingsFolder = "06 - Main Notes/Meetings"; // adjust if needed

const current = dv.current();
// normalize project list from this note's frontmatter
const myListRaw = current.related_projects ?? [];
const myList = (Array.isArray(myListRaw) ? myListRaw : [myListRaw])
  .map(x => String(x).toLowerCase().trim())
  .filter(x => x.length);

if (!myList.length) {
  dv.paragraph("No `related_projects` values found in this note's frontmatter.");
} else {
  // pages that match the project's related_projects OR have matching file tags
  const pages = dv.pages(`"${meetingsFolder}"`)
    .where(p => {
      const rpRaw = p.related_projects ?? [];
      const rp = (Array.isArray(rpRaw) ? rpRaw : [rpRaw])
        .map(x => String(x).toLowerCase().trim());
      const tags = (p.file.tags ?? []).map(t => String(t).toLowerCase().trim());
      const combined = [...rp, ...tags];
      return myList.some(m => combined.includes(m));
    })
    .sort(p => p.file.cday, 'desc');

  let total = 0;
  pages.forEach(p => {
    // Dataview exposes the page tasks as p.file.tasks (or p.tasks) depending on version
    const tasks = (p.file?.tasks ?? p.tasks ?? [])
      .filter(t => !t.completed); // only open tasks

    if (tasks.length) {
      total += tasks.length;
      // show a small header linking to the meeting note
      dv.header(4, p.file.link);
      // render the checklist items (retains the task text, links, block refs)
      dv.taskList(tasks, false);
    }
  });

  if (!total) dv.paragraph("No open tasks found in matching meeting notes.");
}
```
## Meetings
```dataviewjs
// Show meetings that match any of this file's related_projects (frontmatter or tags)
// Configuration:
const meetingsFolder = "06 - Main Notes/Meetings"; // adjust if your folder path differs

// Get current note's related_projects (normalize to lowercase string array)
const current = dv.current();
const myListRaw = current.related_projects ?? [];
const myList = (Array.isArray(myListRaw) ? myListRaw : [myListRaw])
  .map(x => String(x).toLowerCase().trim())
  .filter(x => x.length);

// Nothing to match?
if (myList.length === 0) {
  dv.paragraph("No `related_projects` values found in this note's frontmatter.");
} else {
  // Gather pages in the meetings folder, filter by intersection with current's related_projects OR by tag match
  const pages = dv.pages(`"${meetingsFolder}"`)
    .where(p => {
      // page frontmatter related_projects (array or single)
      const rpRaw = p.related_projects ?? [];
      const rp = (Array.isArray(rpRaw) ? rpRaw : [rpRaw])
        .map(x => String(x).toLowerCase().trim());

      // file tags (Dataview provides file.tags)
      const tags = (p.file.tags ?? []).map(t => String(t).toLowerCase().trim());

      // combined values to test against
      const combined = [...rp, ...tags];

      // check if any of myList appears in combined
      return myList.some(m => combined.includes(m));
    })
    .sort(p => p.file.cday, 'desc');

  // Render table (adjust columns as you like)
  dv.table(
    ["Created", "File", "Summary", "Related"],
    pages.map(p => [
      p.file.cday,
      p.file.link,
      p.summary ?? "",
      p.related_projects ?? p.file.tags ?? ""
    ])
  );
}
```

## Daily Notes
``` dataviewjs
// Show meetings that match any of this file's related_projects (frontmatter or tags)
// Configuration:
const meetingsFolder = "06 - Main Notes/Daily Note"; // adjust if your folder path differs

// Get current note's related_projects (normalize to lowercase string array)
const current = dv.current();
const myListRaw = current.related_projects ?? [];
const myList = (Array.isArray(myListRaw) ? myListRaw : [myListRaw])
  .map(x => String(x).toLowerCase().trim())
  .filter(x => x.length);

// Nothing to match?
if (myList.length === 0) {
  dv.paragraph("No `related_projects` values found in this note's frontmatter.");
} else {
  // Gather pages in the meetings folder, filter by intersection with current's related_projects OR by tag match
  const pages = dv.pages(`"${meetingsFolder}"`)
    .where(p => {
      // page frontmatter related_projects (array or single)
      const rpRaw = p.related_projects ?? [];
      const rp = (Array.isArray(rpRaw) ? rpRaw : [rpRaw])
        .map(x => String(x).toLowerCase().trim());

      // file tags (Dataview provides file.tags)
      const tags = (p.file.tags ?? []).map(t => String(t).toLowerCase().trim());

      // combined values to test against
      const combined = [...rp, ...tags];

      // check if any of myList appears in combined
      return myList.some(m => combined.includes(m));
    })
    .sort(p => p.file.cday, 'desc');

  // Render table (adjust columns as you like)
  dv.table(
    ["Created", "File", "Summary", "Related"],
    pages.map(p => [
      p.file.cday,
      p.file.link,
      p.summary ?? "",
      p.related_projects ?? p.file.tags ?? ""
    ])
  );
}
```
## Resources & References
- **Links**: {{Links to any related docs, articles, or tutorials}}
- **Team Contacts**: {{List key people you might need to consult for questions}}
- **Notes**: {{Any additional context or notes}}
