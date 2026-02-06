---
project_type:
  - Work Project
type: project
created: <% tp.file.creation_date() %>
start_date: <% tp.file.creation_date("YYYY-MM-DD") %>
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
**Tags**: [[Project MOC]]
## Overview
- **Start Date**: `= this.start_date`
- **Due Date**: `= this.due_date`
- **Status**: `= this.status`
- **Priority**: `= this.priority`
- **Billing Code**: 

## Objective
- **Goal**: {{Brief description of what this project aims to achieve}}
- **Client / Team**: `= this.team`

## Steps / Tasks

### General Tasks
- [ ] {{Task 1 description and any sub-tasks or details needed}}
- [ ] {{Task 2 description and any sub-tasks or details needed}}
- [ ] {{Task 3 description and any sub-tasks or details needed}}
### Open Tasks
```dataview
TASK
WHERE (
  file.path = this.file.path OR
  (file.folder = "06 - Main Notes/Meetings" AND contains(related_projects, this.file.link)) OR
  (file.folder = "06 - Main Notes/Daily Note" AND contains(related_projects, this.file.link))
) AND !completed
GROUP BY file.link
SORT file.cday DESC
```
### Completed Tasks
```dataview
TASK
WHERE (
  file.path = this.file.path OR
  (file.folder = "06 - Main Notes/Meetings" AND contains(related_projects, this.file.link)) OR
  (file.folder = "06 - Main Notes/Daily Note" AND contains(related_projects, this.file.link))
) AND completed
GROUP BY file.link
```

## Meetings
``` dataview
TABLE file.cday AS "Created", summary AS "Summary"
FROM "06 - Main Notes/Meetings"
WHERE contains(related_projects, this.file.link)
SORT file.cday DESC
```

## Daily Notes
``` dataview
TABLE file.day AS Day, summary AS Summary
FROM "06 - Main Notes/Daily Note"
WHERE contains(related_projects, this.file.link)
SORT file.day DESC
```
## Resources & References
- **Links**: {{Links to any related docs, articles, or tutorials}}
- **Team Contacts**: {{List key people you might need to consult for questions}}
- **Notes**: {{Any additional context or notes}}
