---
templateKey: blog-post
title: Typesafe React Patterns
date: 2019-01-11T15:17:48.183Z
description: Typesafe templates for common react patterns.
tags:
  - React
  - Typescript
---
# Introduction

This article explains examples of the following common React patterns, written in Typescript:

* Presentational components
* Container components
* Higher Order Components


## Setup

* Language - Typescript
* Framework - React
  * Router - React-Router
  * State - Redux
  * Side effects -  Redux Saga

## Presentational component
```tsx
import * as React from "react";

export default props => <div>Hello {props.name}</div>
```
Ok but we want this to be typed, so we have to do:
1. Give the function parameter `props` a type.
2. Give the type returned by the React.Component class or method. (In this case we are defining our react component as a method.).
3. Give the type of the React.Component.

1 is easiest. We define an interface keyed by parameter names. Within the interface, the type of each parameter can be defined.

```
import * as React from "react";

interface componentProps = {
  name: string
}

export default (props: componentProps) => <div>Hello {props.name}</div>
```
2. is a little more tricky. The question is: "
```
i
