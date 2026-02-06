---
created: "{{date: MMMM Do, YYYY}} {{time}}"
filename: "{{date: YYYY-MM-DD}}"
tag: DailyNotes
related_projects:
summary:
---
**Tags**:: [[Daily MOC]]
# DAILY NOTE
## {{date: dddd, MMMM Do, YYYY}}

---
## Tasks Due Today

```tasks
not done
due on `{{query.file.filenameWithoutExtension}}`
sort by due
```

## Tasks Completed Today
```tasks
done {{query.file.filenameWithoutExtension}}
sort by due
```
---
## Tasks Created Today
```tasks
created on `{{query.file.filenameWithoutExtension}}`
```
---
## 📝 General Notes
- 

---
### Notes Created Today
```dataview 
LIST WHERE file.cday = this.file.day 
```

### Notes Last Touched Today
```dataview
LIST WHERE file.mday = this.file.day 
SORT file.mtime asc
```
