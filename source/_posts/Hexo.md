---
title: Hexo - How to publish site on Github
date: 2017-01-09 22:59:32
tags: Tool
---

It's a tool that supports generating static HTML from Markdown, which makes it suitable to publish personal blog on website (e.g. github-pages).

## Installation
```
npm install -g hexo-cli
```

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

## Theme [TBD]
There is a dedicate topic on Hexo at Zhihu.com