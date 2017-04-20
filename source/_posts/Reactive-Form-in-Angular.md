---
title: Reactive Form in Angular
date: 2017-04-10 19:05:50
categories: Programming
tags:
- Angular
---

# FormControl’s valueChanges 
It gives Observable, which takes us from plain event data into observable data flow. It’s one way for child components to have (local) communication between themselves.

# switchMap vs flatMap
The former unsubscribes from the previous subscriptions as soon as the outer Observable emits new values.

<!-- more -->

# Data binding
It’s possible to use ngModel together with reactive form. To avoid mutate the data property (via two way binding of ngModel), one way is to clone the raw model and adapt it to a view model and bind it to form controls.

# Data flow 
Child components can get a model reference from parent component via @Input. Compute property (e.g. full name from first name + last name) can be provided as a method of component (otherwise it is difficult to detect the input’s change value other than ngOnChanges).
But if the input property is a primitive property like boolean, number or string, the change inside the child component won’t get reflected on its parent, not even with ngModel. The solution is to pass in a reference of an object

# Reference
https://blog.thoughtram.io/angular/2016/01/06/taking-advantage-of-observables-in-angular2.html
