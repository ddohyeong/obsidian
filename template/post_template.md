<%*
let title = await tp.system.prompt("Enter title");
let newFileName = `${tp.date.now("YYYY-MM-DD")}-${title}`;
tp.file.rename(newFileName);
-%>
---
title: <%- title %>
categories:
  - template
tags:
toc: true
toc_sticky: true
date: <% tp.date.now() %>
posting: false

---