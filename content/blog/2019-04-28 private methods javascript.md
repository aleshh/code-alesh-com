---
title: "Private methods with vanilla JavaScript"
date: "2019-04-28"
tags: ["JavaScript"]
---

So you've been using <code>create-react-app</code> and now you want to do a simple web project, maybe with jQuery, but you want quasi-modules with private methods? Here's what briefly worked for me, before I threw in the towel and installed webpack and switched to using <code>import</code> and <code>export</code>.

<!--more-->

With this, <code>Board.create()</code> will work and <code>Board.makeRow()</code> will not.

<pre><code>const Board = (function() {

  // private methods
  const makeRow = (rowId) =&gt; {
    const $row = $('&lt;tr&gt;')
    for (let j = 0; j &lt; boardWidth; j++) {
      const id = cellId(rowId, j)
      $row.append(makeCell(id))
    }
    return $row
  }

  const makeCell = (id) =&gt; $('&lt;td&gt;').attr(&quot;id&quot;, id)

  const cellId = (row, cell) =&gt; {
    let id =  (row &gt; 9) ? &quot;&quot; + row : &quot;0&quot; + row
    id += (cell &gt; 9) ? cell : &quot;0&quot; + cell
    return id
  }

  return {
    // public method
    create: () =&gt; {
      const $board = $('&lt;table&gt;')
      for (let i = 0; i &lt; boardHeight; i++) {
        $board.append(makeRow(i))
      }
      return $board
    }
  }

}())</code></pre>
