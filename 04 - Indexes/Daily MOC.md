[[+Home]] %% tags:: #MOC %% 

# Daily MOC
My daily notes are stored here: `06 - Main Notes/Daily Note`.

**Template:** [[Template, Daily Note]]

```meta-bind-button
label: New daily note
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
    templateFile: 05 - Templates/Template, Daily Note.md
    folderPath: 06 - Main Notes/Daily Note
    fileName: t
    openNote: true
    openIfAlreadyExists: false

``` 

```dataview
TABLE 
	file.name as Date, 
	file.cday as Created, 
	summary as Summary, 
	related_projects as Project
FROM "06 - Main Notes/Daily Note" and -#MOC
SORT file.name DESC
``` 