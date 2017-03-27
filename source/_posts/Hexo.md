---
title: Hexo - How to publish site on Github
date: 2017-01-09 22:59:32
categories: Tool
tags: Hexo
---

It's a tool that supports generating static HTML from Markdown, which makes it suitable to publish personal blog on website (e.g. github-pages).

## Installation
```
npm install -g hexo-cli
```

## Issue of cannot-find-module-DTraceProviderBindings
After playing a hexo project for some time, I happened to try a `yarn install`, which may update some underlying dependencies. Then I found hexo didn't work anymore. The complain is:
```  
Cannot find module './build/Release/DTraceProviderBindings
```

I tried: 
+ `npm i hexo`. No use.
+ `rm -rfd node_modules && npm i`. No use.
+ `npm i hexo --no-optional`. It works for me.
+ `npm uninstall hexo-cli -g && npm i hexo-cli -g`. It should work but I haven't tried it.

## Workflow
```
hexo init <folder>
cd <folder>
hexo clean
hexo n "Title"
hexo g[enerate]
hexo s[erver]
```

## Deploy to github-pages
It's better to create a repo (on github) for the hexo project itself and another repo for the generated HTML files so that we can add contents even on different machines.
```
npm install --save hexo-deployer-git
// add repo, branch setting to config file
hexo d[eploy]
```

## Theme
There is a dedicate topic on Hexo at Zhihu.com
A good starting point is [Next](http://theme-next.iissnan.com)

### How to generate category and tag pages (in Next theme)
By default, even enable these two in menu section of the theme config file, you will still get 404 error when nevigating to "categories" or "tags" pages. The reason is due to the missing index.html under the folder of the above two names. The fix is to add pages with `hexo new page categories` and `hexo new page index`. Then modify the generated index.md to:
```
// index.md for categories
type: "categories"
comments: false

// index.md for tags 
type: "tags"
comments: false
```