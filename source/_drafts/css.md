---
title: CSS Cookbook
date: 2020-09-06 17:33:00
categories: Programming
---

# Sections

| Title |
| --- |
| [Css in general](#CSS-in-general) |
| [Relative units](#Relative-units) |

<!-- more -->

# CSS in general

## Specificity

ID > Class > Tag

Link styles and source order - link, visited, hover, active (LoVe/HAte)
```
a:link {...}
a:visited {...}
a:hover {...}
a:active {...}
```

## Inheritance

color, white-space, line-height, font-xxx, text-xxx, list-xxx are inherited
```
body {
  font-family: sans-serif; // applied to the entire page
}
```

Special values - inherit and initial
```
.footer {
  color: #666;
}

.footer a {
  color: inherit;
}
```

# Relative units

## Using rems for font
```
:root {
  font-size: 0.75em; //12px
}

@media (min-width: 800px) {
  :root {
    font-size: 0.875em; //14px
  }
}

@media (min-width: 1200px) {
  :root {
    font-size: 1em; //16px
  }
}

ul {
  font-size: .8rem
}
```

## Using em for padding and px for border
```
.panel {
  font-size: 1rem; // define font for the panel
  padding: 1em;
  border-radius: 0.5em;
  border: 1px solid #999;
}

.panel > h2 {
  font-size: 0.8em;
}

.panel.large {
  font-size: 1.2rem; // overwrite the font for large panel
}
```

## Viewpoint relative units: vh, vw, vmin
```
.square {
  width: 90vmin;
  height: 90vmin;
}
```

Responsive font size without media query
```
:root {
  font-size:
}
```
