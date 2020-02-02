---
title: "{{ replace (.Name | replaceRE "^[0-9]{4}-[0-9]{2}(-[0-9]{2})?-(.*)" "$2") "-" " " | title }}"
date: {{ .Date }}
draft: true
tags: []
slug: "{{ .Name | replaceRE "^[0-9]{4}-[0-9]{2}(-[0-9]{2})?-(.*)" "$2" }}"
---

