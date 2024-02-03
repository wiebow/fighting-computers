<%*
let qcFileName = await tp.system.prompt("Post Title")
titleName = qcFileName
await tp.file.rename(titleName)
await tp.file.move("/content/" + titleName);
-%>
---
title: "change me"
description:
draft: true
date:
tags:
  - 
---
 
The rest of your content lives here. You can use **Markdown** here :)