---
title: "Private methods with vanilla JavaScript"
date: "2019-04-28"
tags: ["JavaScript"]
---

So you've been using `create-react-app` and now you want to do a simple web project, maybe with jQuery, but you want quasi-modules with private methods? Here's what briefly worked for me, before I threw in the towel and installed webpack and switched to using `import` and `export`.

<!--more-->

With this, `Board.create()` will work and `Board.makeRow()` will not.

```js
const Board = (() => {
  // private methods
  const cellId = (row, cell) => {
    let id = row > 9 ? "" + row : "0" + row
    id += cell > 9 ? cell : "0" + cell
    return id
  }

  const makeCell = id => $("<td>").attr("id", id)

  const makeRow = rowId => {
    const $row = $("<tr>")
    for (let j = 0; j < boardWidth; j++) {
      const id = cellId(rowId, j)
      $row.append(makeCell(id))
    }
    return $row
  }

  return {
    // public method
    create: () => {
      const $board = $("<table>")
      for (let i = 0; i < boardHeight; i++) {
        $board.append(makeRow(i))
      }
      return $board
    },
  }
})()
```
