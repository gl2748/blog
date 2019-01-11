---
templateKey: blog-post
title: Typesafe React Patterns
date: 2019-01-11T15:17:48.183Z
description: Typesafe templates for common react patterns.
tags:
  - React
  - Typescript
---
This article explains the patterns demonstrated in my common react starter.

The common starter is built off \`create-react-app\` and includes the basics to get started with a new react web app.

* Language - Typescript
* Framework - React
  * Router - React-Router
  * State - Redux
  * Side effects -  Redux Saga

It does not have any opinions of Immutable data libraries for javascript, like Immer or Immutable.JS because I have found these difficult and confusing to type. There's no reason not to add immutability if your project needs it.

The patterns covered here are:

* Redux connected Container Components
* High Order Components
