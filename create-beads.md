---
description: "Create beads from a task or plan file."
arguement-hint: <path-to-task-or-plan-file/>
---
  
Create beads out of the provided file. Group the beads into epics if possible.
<critical>Fill the description as precise as possible so that parallel agents can work on tasks whenever possible.</critical>
Specify which file an agent has to touch when working on the bead if that information is available.
If needed link back to a part in the provided file that provides clarification.
Set up dependencies between beads with `bd dep add` where tasks have ordering constraints.
