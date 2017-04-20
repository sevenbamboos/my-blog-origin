---
title: Change Detection in Angular
date: 2017-04-20 19:13:35
categories: Programming
tags:
- Angular
- Zone
---

# The Timing of CD
During asynchronous events:
    Events (button click…)
    XHR requests
    setTimeout

# How CD Works
Trigger from <app-root> and flow one-way down the component tree

<!-- more -->

# Strategy A: Immutable +  ChangeDetectionStrategy.OnPush
CD only happens when the object reference has been changed. If template binding (e.g. methods or variable used by ngIf) won’t be updated, check whether the reference has been changed (or try to mark it as markForCheck with ChangeDetectorRef).

# Strategy B: Observable + ChangeDetectionStrategy.OnPush + ChangeDetectorRef
@Input is an observable
An inner variable is linked to the incoming value (from the stream) via subscribe().
In case CD isn’t triggered (because of no reference change of template binding?), use ChangeDetectorRef::markForCheck()

# Strategy C: Fine-tune ChangeDetectorRef
ChangeDetectorRef::detach() to prevent CD from happening (during large amount of data change).
ChangeDetectorRef::reattach() to enable CD again.

# Nice charts about how different JS libraries do CD from 
http://teropa.info/blog/2015/03/02/change-and-its-detection-in-javascript-frameworks.html

# The Underlying Zone
Zone add hooks (e.g. before, after, error) to async calls.
Angular has its own zone (see NgZone) to trigger CD.
In certain circumstances, it’s convenient to disable CD at some point and enable it after some time to have a better user experience. (see here for an example 
https://blog.thoughtram.io/angular/2017/02/21/using-zones-in-angular-for-better-performance.html )
 
# Reference
https://dzone.com/articles/tuning-angulars-change-detection
https://blog.thoughtram.io/angular/2016/02/22/angular-2-change-detection-explained.html
