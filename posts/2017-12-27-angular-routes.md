---
title: Angular Routes
date: 2017/12/27 18:30:00
image: "/assets/images/posts/angular.jpg"
comments: true
tags: technology, angular
---
<em>NOTE: The method mentioned below is only needed in case your parameters are likely to change after routing to a component.
Otherwise, just use `router.snapshot.paramMap`.</em>

I recently encountered a situation while building an Angular app, where I needed to handle route params and optional params at the same time.
Obviously, I used GET params as the optional parameters, as has been the case for long.
The problem, however, was that Angular Router provides separate `Observables` for handling route params, and query params.

Route Params:
```js
this.router.params.subscribe(params => {
  // do something with route params
});
```

Query Params:
```js
this.router.queryParams.subscribe(params => {
  // do something with query params
});
```
The problem was that I needed both parameters to be resolved before I could start rendering the component.

In a scenario where you have the same component across pages, the component is not re-rendered, or re-initialized.
Which means that you might not receive a callback if you navigate to the same page, 
wanting to the same component with different parameters. The router will not send you a callback if you navigate to the 
same URL either. Which means, you can't wait for both `Observable`s to resolve, because there is no guarantee of both 
resolving.

## The Solution
After a bunch of googling and reading through various blog posts, I was unable to find a good solution to this problem.
Finally, I decided to [RTFM](https://en.wikipedia.org/wiki/RTFM), which in hindsight, I should have done sooner.
In a huge page documenting the router behavior, there are a few nondescript lines that mention optional params.
The Angular team has introduced a new concept called `Matrix parameters` which appear in the URL separated by semicolons.
ParamMaps have been introduced in Angular 4.0.
Turns out, the solution is pretty simple using this approach.

```js
this.router.paramMap.subscribe(params => {
  // do something with optional and required
});
```
Not just that, the [ParamMap](https://angular.io/api/router/ParamMap) class provides nice getter methods methods to make it easier to read values.
Hope this helps someone looking for optional arguments in Angular routes.
