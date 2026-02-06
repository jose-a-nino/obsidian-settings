[[+Home]] %% tags:: #MOC %% 

# Meetings MOC
Meetings are timestamped events with other people, where information is exchanged and collected. Meeting notes are intrinsically ephemeral If there's information in a meeting that needs to be accessed later, it should be moved into a more evergreen note in the Umami folder. My meetings are stored here: `06 - Main Notes/Meetings`.

**Template:** [[Template, Meeting Note]]

```meta-bind-button
label: New meeting
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
    templateFile: 05 - Templates/Template, Meeting Note.md
    folderPath: 06 - Main Notes/Meetings
    fileName: TKTK
    openNote: true
    openIfAlreadyExists: false

``` 

```dataview
TABLE 
	file.cday as Created, 
	summary as Summary, 
	related_projects as Project
FROM "06 - Main Notes/Meetings" and -#MOC
SORT file.cday DESC
```