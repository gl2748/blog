---
templateKey: blog-post
title: Composing Higher Order Components in React.
date: 2019-01-16T15:56:58.177Z
description: >-
  Compose is a functional programming paradigm that makes the execution of the
  transformations that functions effect on data more declarative. It makes these
  transformations easier to reason about by presenting them in a linear way. 
tags:
  - react javascript functional
---
In React we often wrap "Function Components" in "Higher Order Components" in order to inject additional props into them and to cherry-pick some component life-cycle-methods that we want them to make use of.

[Maximizing composability of React HOCs is recommended in the docs](https://reactjs.org/docs/higher-order-components.html#convention-maximizing-composability)

Here we will demonstrate an implementation.

```js
const UserContainer = connect(
    mapStateToProps,
    mapDispatchToProps
)(withOnMount(withLoading(User)));

export default UserContainer;
```

Here we wrap our `User`component in two HOCs `withOnMount` and `withLoading`, where withOnMount is an HOC that adds a lifecycle method and withLoading displays a loading animation while our User component has a loading prop of true. All this is nice but it's nested and messy, it has an opinion of the order in which our component is wrapped, where in fact we're agnostic. This would be nicer:

```
const withState = connect(mapStateToProps, mapDispatchToProps)
export default compose<any, any, any, any>(
    withState,
    withOnMount,
    withLoading
)(User);
```
Here our enhancements are composed together and applied against the deepest nested function from the earlier example. Esentiall we turned: 

```
const result = a(b(c))
```
into
```
const result = compose(a,b)(c)
```
where
```
const compose = (...fns) => fns.reduce((f, g) => (...args) => f(g(...args)));

```

The key to achieving this is the [compose method, which we will take from Ramda](https://ramdajs.com/docs/#compose), and ensuring each of our HOCs has the function signature of only taking in one parameter, of the same type returned by the preceding function. In our case:
`Component => Component` In fact, with compose generally, the rule is that the first method have the maximum number of allowed arguments.


## Bonus - DIY compose

Here is a little codesandbox of the three passes I took at writing a compose function of my own, when exploring the concepts in this post.

<iframe src="https://codesandbox.io/embed/815862qxz0" style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;" sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"></iframe>
