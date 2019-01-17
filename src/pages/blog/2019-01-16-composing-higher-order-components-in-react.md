---
templateKey: blog-post
title: Composing Higher Order Components in React.
date: 2019-01-16T15:56:58.177Z
description: >-
  Compose is a functional programming paradigm that makes the execution of the
  transformations that functions effect on data more declarative. It makes these
  transformations easier to reason about by presenting them in a linear way.
  e.g.
tags:
  - react javascript functional
---

```js
const x = 10

const add = p => q => p + q;

const add1 = add(1)
const add2 = add(2)

// Note that here the accumulating value is itself a function.
const reducer = (acc, curr) => (...args) => {
  return curr(acc(...args));
};

const compose = (...fns) => fns.reduce(reducer);

const calc = compose(
  add1,
  add2
)(x)

// calc = 13
```

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
Here our enhancements are composed together and applied against the deepest nested function from the earlier example. Essentially we turned: 

```
const result = a(b(c))
```
into
```
const result = compose(a,b)(c)
```
where (See the codesandbox below or how I rolled my own compose.
```
const compose = (...fns) => fns.reduce((f, g) => (...args) => f(g(...args)));

```

The key to achieving this is the [compose method, which we will take from Ramda](https://ramdajs.com/docs/#compose), and ensuring each of our HOCs has the function signature of only taking in one parameter, of the same type returned by the preceding function. In our case:
`Component => Component` In fact, with compose generally, the rule is that the first method have the maximum number of allowed arguments, with subsequent composed methods being unary.

Finally the question becomes resolving the types for each method passed to `compose`.


## Bonus - DIY compose

Here is a little codesandbox of the three passes I took at writing a compose function of my own, when exploring the concepts in this post.

Some interesting discoveries were:
Getting the compose function to accomodate more-than-one argument in the initial function.


Achieving this in the final version is a reduce function much like the earlier versions, except that the reducer has as its accumulator another function. Basically "The accumulating value is a function and it's growing the closure scope."

> Since return values in JS can only be a single value (unlike, say, Go), I think it accommodates for that initial function. Subsequent functions must share the signature x => y.

Finally, with this reducer, we have a segue into transducers - or reducers that make new reducers.

<iframe src="https://codesandbox.io/embed/815862qxz0" style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;" sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"></iframe>
