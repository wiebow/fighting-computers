<%*
let qcFileName = await tp.system.prompt("Post Title")
titleName = qcFileName
await tp.file.rename(titleName)
await tp.file.move("/content/" + titleName);
-%>
---
title: "<% titleName %>"
draft: true
tags:
  - 
---
 
The rest of your content lives here. You can use **Markdown** here :)