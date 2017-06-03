---
title: MVC, MVP, MVVM and Redux
date: 2017-05-15 22:17:20
categories: Programming
tags: 
- MVC
- MVP
- MVVM
- Redux
---

# Problem
- View controller is getting bigger and bigger
- View controller keeps data (or cached data)
- View controller is NOT testable
- View controllers tend to have complicated interaction
- Model is dumb data structure

<!--more-->

# MVC came to help
{% asset_img "MVC.png" "MVC" %}

# MVC evolved into MVP and MVVM 
{% asset_img "MVP_MVVM.png" "MVP and MVVM" %}

The point of MVP is to extract UI independent logic out of UI controller, while MVVM's effect is to decouple the dependency between view and view model, view and model

# MVVM + Redux, Best Practice?
{% asset_img "MVVM_Redux.png" "MVVM + Redux" %}

# Reference
[GUI Architectures](https://martinfowler.com/eaaDev/uiArchs.html)
[MVC|MVP|MVVM](https://juejin.im/post/593021272f301e0058273468)
