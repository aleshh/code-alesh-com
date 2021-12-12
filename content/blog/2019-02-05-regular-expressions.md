---
title: "Regular expressions shenanigans"
date: "2019-02-05"
tags: ["RegEx"]
---

I'm working on an app for exploring the Dewey Decimal System. It's based on three-digit numbers in which each number is a different level of specificity. The 700's are arts, 770's are photography, 774 is holography. I started with a text file with all the codes and their descriptions and made a flat array of objects like

```
[
  { "number": "000", "description": "Generalities " },
  { "number": "001", "description": "Knowledge " },
  { "number": "001.5", "description": "Communications " },
...
```

Which worked fine for searching, but we also want to browse. That is, see the 10 top categories, select one, see its subcategories, etc. So what's required is a tree of nested objects. <!--more--> <a href="https://sites.google.com/site/pclcat/Home/procedures/dewey-decimal-classification">This page</a> has the codes we need, and if we copy paste and do a little cleanup we get a nicely indented list like so:

```
000 Generalities
  000 Generalities & computer science
    001 Knowledge
      001.1 Intellectual Life
      001.2 Scholarship and Learning
      001.3 Humanities
      001.4 Research
      001.9 Controversial knowledge
    002 The book
    003 Systems
...
```

Which we need to convert to JSON, with every line looking like the code above, except that indented items are an array within the object above them, like so:

```json
[
  { "number": "000", "description": "Generalities", "subordinates": [
    { "number": "000", "description": "Generalities & computer science", "subordinates": [
      { "number": "001", "description": "Knowledge", "subordinates": [
        { "number": "001.1", "description": "Intellectual Life" },
        { "number": "001.2", "description": "Scholarship and Learning" },
        { "number": "001.3", "description": "Humanities" },
        { "number": "001.4", "description": "Research" },
        { "number": "001.9", "description": "Controversial knowledge" }
      ]},
      { "number": "002", "description": "The book" },
      { "number": "003", "description": "Systems" },
...
```

We're doing regular expression search and replace in Visual Studio Code. I'm a RegExp amateur, but I did it in three steps like this:

<b>Step 1:</b> replace each line with object notion, preserving indentation:

```
Search:  ^( *)([0-9]{3}\.*[0-9]*) ?(.*)$
Replace: $1{ "number": "$2", "description": "$3" },
```

<b>Step 2:</b> Here's the tricky bit. Starting from the outside and working in, we look for bits indented by 4 or more spaces, replace the object close on the previous line and replace it with a new property and an array opening and insert the array and object close afterwards:

```
Search:   },\n((   +{ "number": "[0-9.]*", "description": "[A-Za-zæçé 0-9\.&\(\)/\';,\[\]-]*" },\n)+)
Replace: , "subordinates": [\n$1  ]},\n
```

We've got three levels of indents, so we need to repeat this three times, each time increasing the number of spaces (in the search after the <code>((</code> and in the replace after the <code>$1</code>) by two. (It's easier than it looks to write these because the editor highlights matches.)

<b>Step 3:</b> Now all we need to do is remove the commas after the final item in ever array, which is invalid JSON:

```
Search:  ,\n( +)]
Replace: \n$1]
```
