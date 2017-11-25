---
title: Angular Update Note
categories: ['temp']
---


## 2016-10

### Angular 2.1.0 now available

- Router now supports preloading of lazy loaded modules
- Animation in Angular has been enhanced by adding  :enter and :leave aliases for the commonvoid => * and * => void state changes. and transition API updated

### versioning and releasing angular

## 2016-11

### Angular 2.2.0 now available

- AOT compile your Angular 2 Components and Modules when using @angular/upgrade
- added features to the router to assist with version 1.x to 2.x migrations.
- Code generated from AOT compilation (NgFactories) will now be smaller forms
- added guides on using Angular with ES5 and ES6/7.

## 2016-12

### Angular 2.3.0 now available

- releasing the first version of the Angular Language Service. （integrated with IDE and provide error checking and type completion with Angular Templates）
- take advantage of object inheritance for components
- latest release of zone.js includes improved stack traces.

### ok...let me explain: going to be angular 4.0


#### Angular uses SEMVER
> Whenever you fix a bug and release it, you increase the last number, if a new feature is added, you increase the second number and whenever you release a breaking change you increase the first number.
#### Breaking changes don’t have to be painful!
>  from Angular 1 to Angular 2, and it was a total breaking change, with new APIs, new patterns. That was obvious: ultimately Angular 2 was a complete rewrite.
> Changing from version 2 to version 4, 5, … won’t be like changing from Angular 1.
>  simply be a change in some core libraries that demand a major SEMVER version change
> Angular team uses a tool for handling automatic upgrades, even of breaking changes
#### It’s just “Angular”
> That said, we should start naming it simply “Angular” without the version suffix.
#### Naming guidelines
> “This article uses Angular v2.3.1.”
> Try to avoid using the version number
#### Why not version 3 then?
> core Angular libraries live in one single GitHub repository at github.com/angular/angular. All of them are versioned the same way, but distributed as different NPM packages:
>  all the core packages are aligned which will be easier to maintain and help avoid confusion in the future.
> Due to this misalignment of the router package’s version,
> decided to go straight for Angular v4.
#### Tentative release schedule
> patch releases every week,3 monthly minor release after each major release and a major release with easy-to-migrate-over breaking changes every 6 months.
#### Conclusion
> don’t worry about version numberswe do need to evolve Angular in order to avoid another Angular 1 to Angular 2 change, but we should do it together as a community in a transparent, predictable and incremental way.
- source: [Angular: Ok... let me explain: it's going to be Angular 4.0, or just Angular](http://angularjs.blogspot.com/2016/12/ok-let-me-explain-its-going-to-be.html)



### Angular 2.4.0 now available

- This release updates our dependencies to the recently announced RxJS 5 stable


### angular material beta release

- @angular/material includes 22 UI components written for the latest Angular
- the new @angular/flex-layout package is a general-purpose flex-based layout library
- built all of the core UI components that most applications will need.
- Angular Material, developers can expect other advanced components (e.g. data-table, date-picker), and typography support.

## 2017-01

understanding AOT and dynamic components

## 2017-03

### 2017-03-23 Angular 4.0.0 available

（4.0 成为第一个长期支持版本）

#### Smaller & Faster
> By no means are we done yet, and you'll see us being focused on making further improvements in the coming months.
##### View Engine
> hood to what AOT generated code looks like. These changes reduce the size of the generated code for your components by around 60%  
> about what we did with the View Engine.
> View Engine
##### Animation Package
>  pulled animations out of @angular/core and into their own package.
> Animation Package
#### New Features
##### Improved *ngIf and *ngFor
> Improved *ngIf and *ngFor
> use an if/else style syntax, and assign local variables
##### Angular Universal
> Angular Universal
> now up to date with Angular again, and this is the first release since Universal, originally a community-driven project, was adopted by the Angular team.
> The majority of the Universal code is now located in @angular/platform-server.
> renderModuleFactory method in @angular/platform-server,
##### TypeScript 2.1 and 2.2 compatibility
> TypeScript 2.1 and 2.2 compatibility
##### Source Maps for Templates
> Source Maps for Templates

#### What's next?
> We are going to continue making Angular smaller and faster, and we're going to evolve capabilities such as @angular/http, @angular/service-worker, and @angular/language-service out of experimental.
>backwards compatible with 2.x.x for most applications.
> working on for the past 3 months
- source: [Angular: Angular 4.0.0 Now Available](http://angularjs.blogspot.com/2017/03/angular-400-now-available.html)



## 2017-04

### 2017-04-10: Official languages at Google

Dart has been used for unrestricted client development at Google now for 4+ years. Dart and AngularDart are used by large products such as AdWords, AdSense and Shopping, and by critical internal tools such as Google CRM. In addition, the Flutter cross-platform mobile app framework uses Dart and is used by multiple teams at Google including Google CRM and Shopping Express. The Google codebase contains many millions of lines of Dart code.

Typescript has become allowed for unrestricted client development as of March 2017. TypeScript and Angular on TypeScript are used in Google Analytics, Firebase, and Google Cloud Platform and critical internal tools such as bug tracking, employee reviews, and product approval and launch tools.

both are  allowed to be used for client side development.
