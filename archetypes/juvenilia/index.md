---
title: "{{ replace .File.ContentBaseName "-" " " | title }}"
date: {{ .Date }}
tags: []
draft: true
# Put cover.jpg (thumbnail) and page-01.jpg, page-02.jpg … next to this file.
# Keep the gallery shortcode in the text below to show every page (see README).
---

Age, medium, a line about it.

{{ "{{< gallery >}}" }}
