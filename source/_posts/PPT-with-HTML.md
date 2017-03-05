---
title: PPT with HTML
date: 2017-02-13 22:29:32
categories: Tool
tags:
- webslides
- PPT
- html
- javascript
---

### How to set up
Download the bundle from [webslides](https://github.com/jlantunez/webslides)

### Sample can be found in index.html.
Here is a simple page for title:
``` html
<section>
  <div class="wrap aligncenter">
    <h1><strong>Title</strong></h1>
    <p class="text-intro">Subtitle <br> some introduction to the topic</p>
  </div>
</section>
```

A contents page:
``` html
<section>
  <div class="wrap">
    <h2>Contents</h2>
    <ul class="flexblock features">
      <li>
        <div>
          <h2>Section 1</h2>
          Section 1 details
        </div>
      </li>
      <li>
        <div>
          <h2>Section 1</h2>
          Section 1 details
        </div>
      </li>
    </ul>
  </div>
</section>
```

A page with points:
``` html
<section>
  <div class="wrap size-50 aligncenter">
    <h2><strong>Section with points</strong></h2>
    <p class="text-intro">Section 1 introduction</p>
    <div class="bg-white shadow">
      <ul class="flexblock reasons">
        <li>
          <h2>Point 1</h2>
          <p>Point 1 reason</p>
        </li>
        <li>
          <h2>Point 2</h2>
          <p>Point 2 reason</p>
        </li>
        <!-- It's better not to put too many points on one slide, especially for the demo on small screen devices -->
      </ul>
    </div>
    <!-- .end .bg-white shadow -->
  </div>
</section>
```

A page with two columns:
``` html
<section>
  <!--.wrap = container 1200px -->
  <div class="wrap">
    <div class="grid vertical-align">

      <div class="column">
        <h3><strong>Section in two columns</strong></h3>
        <p class="text-intro">Put source code <code>System.out.println("Hello world");</code> in the middle of text with <strong>strong</strong> effect.</p>
      </div>
      <!-- .end .column -->

      <div class="column">
<!-- remove the leading space for source code to get a better format -->
        <pre>
let greeting = n => `Hello, ${n}`;
let print = console.log;
print(greeting("Sam"));
        </pre>
      </div>
      <!-- .end .column -->

    </div>
    <!-- .end .grid -->

  </div>
  <!-- .end .wrap -->

</section>
```

A last-word page:
``` html
<section class="slide-bottom">
  <div class="wrap">
    <div class="content-right text-serif">
      <h2><strong>One more thing</strong></h2>
      <p>
        I still have something to say ...
      </p>
    </div>
    <!-- .end .content-right -->
  </div>
  <!-- .end .wrap -->
</section>
```