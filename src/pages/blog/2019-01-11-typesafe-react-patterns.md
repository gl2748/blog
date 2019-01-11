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
Ok but we want this to be typed. We think of the component in terms of what goes into it, what comes out of it, and in what circumstances it is to be used.

Thinking of our component in these terms, it is clear that we should:
1. Give the function parameter `props` a type.
2. Give the type returned by the React.Component class or method. (In this case we are defining our react component as a method.)
3. Give the type of the react component we are writing.


---
1. is easiest. We define an interface keyed by parameter names. Within the interface, the type of each parameter can be defined. Voila, if you're familiar with defining the propTypes of your components this looks like it does the same thing, no?

```
import * as React from "react";

interface componentProps = {
  name: string
}

export default (props: componentProps) => <div>Hello {props.name}</div>
```
2. is a little more tricky. The question is: "What does React do with this dom element that we are returning..." And the answer is to get your type from react, asking ourselves:

```React.someReactElementType<paramsInterfaceForThatType>```
```
import * as React from "react";

interface componentProps {
    name: string
}

const myComponent = (props: componentProps): React.ReactElement<any> =>
    <div>
        Hello {props.name}
    </div>

export default myComponent;

```

So that type adds some assurance of what comes out of our method, but doesn't go beyond saying: "This is a react element", what if we could explicitly specify the type of react element it is (Class or Method), and furthermore the types of params, that element takes? Well it's not that simple, inspecting the interface `ReactElement` reveals:
```
interface ReactElement<P> {
    type: string | ComponentClass<P> | FunctionComponent<P>;
    props: P;
    key: Key | null;
}
```

React obviously infers and assigns the type, along with the key, letting us have some say over the Props, ultimately passed to the component.

This is interesting because we can see in the types of the codebase where our work and the React framework interface. Furthermore, how React apportions responsibilities points to good patterns to follow. Basically this type says:
 > "Hey buddy, you focus on the props of the component, make sure that's defined, we've got you for the rest of what makes up the component. And besides your options are limited, class component or function component or a string :-) Go look them up..."

With this in mind our component becomes:
```
import * as React from "react";

interface componentProps {
    name: string
}

const myComponent = (props: componentProps): React.ReactElement<componentProps> =>
    <div>
        Hello {props.name}
    </div>

export default myComponent;
```

3. Is last, what is our component made up of, this is what we use to determine where it gets used. 



NB. Remember how I said it looks like the interface for our props is a bit like propTypes on the react element ?
