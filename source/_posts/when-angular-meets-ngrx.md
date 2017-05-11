---
title: When Angular meets Redux
date: 2017-03-24 22:33:00
categories: Programming
tags:
- Angular
- typescript
- Redux
- NgRx
- RxJS
---

## Introduction
It's quite a long time after the last post. Although keeping learning in a systematic way in this period, I find it more difficult than expected to come up with a post with decent insight. Fortunately, I got an assignment in the workplace to build a prototype GUI with Angular technology. It's a good opportunity to make use of the front-end knowledge from books as well as the articles from the Internet. It's the major source to let me write down the following words of my feeling and understanding when working in an Angular project.

As usual, I'm used to getting the development environment ready before getting my hands into code. I have a bit experience of AngularJS, which reminds me of how a heavyweight framework AngularJS is, especially comparing with other frameworks (e.g. Vue) or libraries (e.g. JQuery). An obvious indication of this fact is that I need lots of boilerplate configuration and code to prepare the project before I can start coding the first line of business logic. Angular (2) improves in this area with a powerful command line tool, @angular/cli (previously known as angular-cli). So we'll start from this tool.

<!-- more -->

## @angular/cli
I tried angular-cli beta and @angular/cli release candidate version 1. At the time of writing this post, Angular team has announced Angular 4.0.0 as well as @angular/cli 1.0.0. For now, it still has some compatible issue with @ngrx/store, which I'll talk about later for the details. As a result, I'll still use the RC-1.0 version.
```
npm i --global @angular/cli
```

Quite embarrassing, but it tends to have trouble when downloading node-sass's binary file (I believe you may know the root cause is Amazon cloud service is NOT accessible from this country), which could happen both on the installation of @angular/cli itself and the moment when doing `npm install` in the Angular project. The solution is to download the file in advance from [taobao's npm mirror](npm.taobao.org/mirrors) and:
```
// For local installation of each project, remove the global flag.
npm i --global node-sass --sass-binary-path=<placeholder>
```

More often than normal, if there is the above problem, npm also needs a proxy to make it feasible: `npm config set -g proxy http://<host>:<port>`. Alternatively, it's more convenient to use nrm: `nrm ls` and `nrm use`.

Once @angular/cli is ready, the use is quite straightforward:
```
// Create a new project. 
ng new --skip-install true --skip-tests true --routing true --inline-style <project-name>
```
`skip-install` is due to the above reason of node-sass. It saves time to install it manually first and then do the installation of other packages.

`skip-tests` is because I find the generation of spec files for each code file quite annoying. But it depends on personal preference and in general I don't see many benefits to have all GUI code covered by unit test. Maybe it has value to have unit test for some service or model files which have complicated logic. It can be added later as I wish.

`routing` can be helpful but not necessary for every project. To be honest, routing module tends to be straightforward in many projects but the implementation differs from framework to framework and therefore hard to learn by heart. That's the reason I add it here to save some time.

`inline-style` makes the tool not create a style file for each component, which can make the source code folder bloat really faster and hard to locate the really useful staff. What's more, most of time the inline style tends to be short enough to be suitable to put along with the main component file.

```
// Add module
ng g module --spec false name
```
I like the idea to have a flat structure for source code files and generally I recommend to have a store module and a module for each domain to put UI components. Inside the store module, each domain can have its own folder and for each domain module, each routing page which tends to be the top-level component can have its own folder to put its children components. It can have a share/util/core module to share something for multiple domain modules. I'll explain the store module in @ngrx/store part.

```
// Add class (model/utility)
ng g class --spec false folder/name
```
Again, it depends whether there is a need for unit test.

```
// Add component
ng g component --flat true --inline-style true --spec false --app <app-name> folder/name
```
If a component uses inline-style (and there is no unit test), then it has at most two files: one for source code, the other for template. As a result, we can use `flat` not to wrap it in a new folder. Note, as same as module, the new component will have a "component" suffix so it's unnecessary to include it in the command line argument.

Regarding adding styles to @angular/cli, first we need to install the style with:
`npm i --save bootstrap@next`, for now it will install a beta version of bootstrap 4.0. Then add reference to the "styles" section of angular-cli.json:
`../node_modules/bootstrap/dist/css/bootstrap.min.css`. It starts the root folder from src, and that's the reason I put `..` at the beginning.

## Typescript
Normally, with the help of @angular/cli, there is no need to configure Typescript. However, for Node projects, there is something to do to make Typescript compiler understand external libraries (which are not written in Typescript) as well as provide indication to IDE. 

For example, to program Node in Typescript, first we need the type definition of Node: `npm i @types/node --save-dev`. 

Then add the reference to the beginning of source file: 
`///<reference path="../node_modules/@types/node/index.d.ts"/>`. 
However, there is warning of this way of adding reference when compiling the source file and I've figured out no better way to walk around.

It also needs a special syntax to import package from such referenced library: `import net = require("net");`.

## IDE
It's rather easy part to explain since each programmer has personal preference on the choice of IDE. A normal choice is Visual Code since it's doing well with Typescript (as well as normal Javascript and Node project). I install several extensions that make the editor even more suitable to me:
+ Vim
+ Material Icon Theme
+ Sublime Material Theme
+ HTML CSS Class Completion
+ SpellChecker

The last spelling check is an offline one and I think it's not as good as that shipped with Vim. I use it only for text editing and may change it with a better one.

I also have a personalized settings:
```
    "editor.wordWrap": "on",
    "editor.tabSize": 2,
    "editor.fontSize": 14,
    "editor.minimap.enabled": true,
    "editor.fontFamily": "Menlo, Monaco, 'Courier New', monospace",
    "workbench.welcome.enabled": false,
    "workbench.iconTheme": "material-icon-theme",
    "workbench.colorTheme": "Sublime Material Theme - Dark",
```

## @ngrx/store and @ngrx/effects
It's a big jump from AngularJS to Angular (2). But what makes it really astonishing is the adaption of Redux. @ngrx is an implementation that fulfills the idea of redux and at the same time make good use of reactive programming (RxJS in Angular).

The basic idea is to keep all business data and UI data in the store and use reducer to make change to these datum. UI components register the interesting part of the store during initialization and dispatch requests to the store to trigger UI updates during user interaction. Note that reducer can't make change directly to a property of the state in the store because there is no way to do dirty check for property change in Javascript. On the contrary, a new array or a new object should be created and replace the original state in the store. For the performance concern, the references inside the array or object should be reused as many as possible from the original state if no change. 

@ngrx/store can be installed with `npm install @ngrx/core @ngrx/store --save`. To help monitor the state in the store after each reducer, we can install a helper plugin `npm install @ngrx/store-devtools --save`. It needs an extension of Chrome browser about Redux (not sure if there is a counterpart for Safari).

Also note that each reducer is a pure function which can't have any side effects like ajax call or console log. As a result, @ngrx/effects comes to help `npm i @ngrx/effects --save`. Components ask effects to do certain things with side effects, which then dispatch the consequence (via success/fail action) to reducers to continue.

The final code structure is that the service classes become very thin (or disappear at all) and components have no dependency to services. Instead, they only dispatch requests to store and effects intercept some of them (with side effects), do the side effects and then delegate to reducer to finish the work. It also lowers the necessary of exposing `@Output()` for children components. That is to say, container components don't have to become the delegate of children components and children can directly send request to the (global) store. As a result, there is no inter-change of messages between components to make the project easy to maintain in the long term.

I publish part of the prototype project at [here](git@github.com:sevenbamboos/bpe-tool.git). Please refer to it for more details.

## Conclusion
Angular with @ngrx/store is a superb framework that makes the development of mid-sized front-end projects quite easy with an acceptable learning curve (comparing with AngularJS). However, I'm not saying it's a silver bullet for any sophisticated products which find their way in more flexible libraries like VueJS or React.
