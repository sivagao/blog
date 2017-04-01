learnangular2.com

## Intro

```shell
Angular 2 is the next version of Google's massively popular MV* framework for building complex applications in the browser (and beyond).

Angular 2 comes with almost everything you need to build a complicated frontend web or mobile apps, from powerful templates to fast rendering, data management, HTTP services, form handling, and so much more.

We hope these simple tutorials help you get up and running with Angular 2 quickly, which cover the basics of using Angular 2, but also ES6 and TypeScript.
``` 

## Why

```shell
When Angular 2 was announced in October 2014 at the ngEurope conference
a faster, more powerful, cleaner, and easier to use tool than we had with Angular 1. We found a tool that embraced future web standards and brought ES6 to more developers around the world.

In many ways, Angular 2 still felt like Angular, it was just based on new JavaScript standards and using best-practices developed in the years after the first Angular release. "Angular" had become a design pattern with multiple implementations.

We hope you find Angular 2 as inspiring as we have, and we hope this guide helps you get there as quickly as possible.
```

## 组件

```shell
在Angular2中，组件是构建和申明页面中元素和逻辑的主要部分。

In Angular 1, we achieved this through directives, controllers, and scope. In Angular 2, all those concepts are combined into Components.

A SIMPLE COMPONENT

Here's a simple Component that renders our name, and a button that triggers a method to print our name to the console:

When we use the <my-component></my-component> tag in our HTML, this component will be created, our constructor called, and rendered.

```

```js
import {Component} from 'angular2/angular2'

@Component({
  selector: 'my-component',
  template: '<div>Hello my name is {{name}}. <button (click)="sayMyName()">Say my name</button></div>'
})
export class MyComponent {
  constructor() {
    this.name = 'Max'
  }
  sayMyName() {
    console.log('My name is', this.name)
  }
}
```

## 应用生命周期

Angular apps go through a multi-stage bootstrap and lifecycle process, and we can respond to various events as our app starts, runs, and creates/destroys components.

### BOOTSTRAP

Angular 2 apps (currently) need to be bootstrapped using the root component for the app.

This component is where you can put application-level code and configuration, and its template is where the whole app component chain gets created.

### COMPONENT INIT

When a component is created, its constructor is called. This is where we initialize state for our component, but if we rely on properties or data from child components, we need to wait for our child components to initialize first.

To do this, we can handle the ngOnInit lifecycle event. Optionally, we could call setTimeout in our constructor for a similar effect:

COMPONENT LIFECYCLE

Like ngOnInit, we can track several events through the lifecycle of a component. For a full list, see the official Angular 2 Lifecyle Hooks docs.

如ngOnCheck, destroy, ngAfterContentInit, ngAfterViewInit等等


## 模板

{}: RENDERING
To render a value, we can use the standard double-curly syntax:
Pipes, previously known as "Filters," transform a value into a new value, like localizing a string or converting a floating point value into a currency representation:

[]: BINDING PROPERTIES
To resolve and bind a variable to a component, use the [] syntax. If we have this.currentVolume in our component, we will pass this through to our component and the values will stay in sync:

(): HANDLING EVENTS
To listen for an event on a component, we use the () stynax

[()]: TWO-WAY DATA BINDING
To keep a binding up to date given user input and other events, use the [()] syntax. Think of it as a combination of handling an event and binding a property:
(从而你的组件和值会stay in sync with input value)

*: THE ASTERISK
* indicates that this directive treats this component as a template and will not draw it as-is. For example, ngFor takes our <my-component> and stamps it out for each item in items, but it never renders our initial <my-component> since it's a template:

## 事件

Events in Angular 2 use the parentheses notation in templates, and trigger methods in a component's class. For example, assume we have this component class:

`<button (click)="clicked()">Click</button>`
Our clicked() method will be called when the button is clicked.

DELEGATION

Events in Angular 2 behave like normal DOM events. They can bubble up and propagate down. Nothing special to do here!

EVENT OBJECT
To capture the event object, pass $event as a parameter in the event callback from the template:

`<button (click)="clicked($event)"></button>`
This is an easy way to modify the event, such as calling preventDefault:

## 表单

http://learnangular2.com/forms/

Forms are the cornerstone of any real app. In Angular 2, forms have changed quite a bit from their v1 counterpart.
Where we used to use ngModel and map to our internal data model, in Angular 2 we more explicitly build forms and form controls.
While it feels like more code to write, in practice it's easier to reason about than with v1, and we no longer have to deal with frustrating ngModel and scope data problems.

SIMPLE FORM
FORMBUILDER
FORM DIRECTIVES
CUSTOM VALIDATORS
HANDLING FORM VALUES


## 语言

One of the most difficult things for developers new to modern JavaScript is how to actually write modern JavaScript.

There's ES5, ES6, then ES7, TypeScript, AtScript, Dart, Babel...the list goes on.

JavaScript is largely a standards-driven language. That means a committee agrees on what "JavaScript" is, at least the lowest common denominator of it, and browser vendors work to implement those features. Today (but not for long), ES5 is that version of JS that is most widely supported.

However, committee-driven design is notoriously slow, so everyone from independent developers to browser vendors are eager to use and implement new JS features faster than the standards organization can approve them.

JavaScript as browsers understand it is a bit like the "assembly of the web," meaning that it can correctly run code that was implemented in a higher level language and "compiled" down to JS the browser understands.

That is exactly what CoffeeScript was, which was one of the first very successful higher-level JS languages. A developer would write CoffeeScript, and the compiler tool would generate plain JavaScript underneath.

This is what we see today with ES6 and everything in between. Browsers don't yet implement natively a lot of ES6+ features, and developers want to innovate on what JavaScript is. This means they've gone to building these higher-level JS languages like AtScript, TypeScript, and tools like Babel that implement futuristic JavaScript features and compile down to ES5.

Dart is an experimental language created several years ago by Google. We do not recommend using Dart as new JS features supersede it.

AtScript was an experimental language created by Google to extend JS and Typescript with new features such as annotations and type introspection. It is now defunct.

Typescript is Microsoft's extension of JS that comes with powerful type checking abilities and object oriented features. Both Angular 2 and Ionic 2 use TypeScript.

ES6 is the next version of JavaScript that was just recently approved that comes with a ton of new ways to write JS. ES7 is a future standard of JS that some are already implementing in these higher level languages.

CONCLUSION
If you'd like to develop with "plain" ES6 and ES7, you can use Babel, the "compiler for writing next generation JavaScript." If you'd like to use Ionic and Angular, we recommend TypeScript which will provide similar features as babel, with extra type checking if you choose to use it.

If you're interested in type checking and new OO features, or want to contribute to Angular 2, check out TypeScript.



