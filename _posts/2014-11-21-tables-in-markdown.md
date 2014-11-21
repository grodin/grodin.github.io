---
layout: post
title: "Tables in Markdown"
description: ""
category:
tags: ["markdown"]
---

A short post to remind myself how to write tables in GitHub Flavoured Markdown
(GFM), hence, on this blog.

Basically, it comes down to using `-----` to separate the header row from the
rest of the rows, and separating columns using `|`.

E.g.

```
| First column         | Second column      | Last column      |
|----------------------|--------------------|------------------|
| First row, first col | 1st row, 2nd c     | Something else   |
| _Second row_         | isn't this **nice**| 2nd row, last col|
| Third row            | ~~strikethrough~~  |                  |
```
renders as

| First column         | Second column      | Last column      |
|----------------------|--------------------|------------------|
| First row, first col | 1st row, 2nd c     | Something else   |
| _Second row_         | isn't this **nice**| 2nd row, last col|
| Third row            | ~~strikethrough~~  |                  |

As this demonstrates, you can use normal markdown within table cells, though as
usual with markdown, block level elements probably don't nest well.

Alignment for columns is specified using `:` in the header separator row:

```
| Left aligned      | Centre aligned        | Right aligned |
|-------------------|:---------------------:|--------------:|
| Left data         | Centre data           | Right data    |
```

renders as

| Left aligned      | Centre aligned        | Right aligned |
|-------------------|:---------------------:|--------------:|
| Left data         | Centre data           | Right data    |

Finally, you don't need to nicely align the raw markdown in order for the table
to be nicely aligned when it's rendered. It just makes it nicer when reading the
raw, which is most of the point of markdown, after all!