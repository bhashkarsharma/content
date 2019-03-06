---
title: Angular Routes And Optional Params
path: '/2017/12/27/angular-routes-optional-params/'
date: '2017-12-27T18:30:00Z'
image: './imgs/angular.jpg'
comments: true
categories: Technology
tags: angular routes matrix
---

<em>NOTE: The method mentioned below is only needed in case your parameters are likely to change after routing to a component.
Otherwise, just use `router.snapshot.paramMap`.</em>

I recently encountered a situation while building an Angular app, where I needed to handle route params and optional params at the same time. Obviously, I used GET params as the optional parameters, as is the standard on the web. However, it turns out that the Angular Router provides separate `Observables` for handling route params, and query params.

Route Params:

```js
this.router.params.subscribe((params) => {
  // do something with route params
})
```

Query Params:

```js
this.router.queryParams.subscribe((params) => {
  // do something with query params
})
```

In a scenario where you have the same component across pages, the component is not re-initialized. Which means that you might not receive a callback if you navigate to the same page, or a different page with different URL parameters, pointing to the same component. The router will not send you a callback if you navigate to the same URL either. Which means, you can't wait for both `Observable`s to resolve, because there is no guarantee of both resolving.

The problem was that I needed both parameters to resolve before I could start rendering the component.

### The Solution

After googling and reading through various blog posts, I was unable to find a good solution to this problem. Finally, I decided to [RTFM](https://en.wikipedia.org/wiki/RTFM), which in hindsight, I should have done sooner. In a [huge page](https://angular.io/guide/router) documenting the router behavior, there are a few lines that mention this scheme. The Angular team has made use of [Matrix URL notation](https://www.w3.org/DesignIssues/MatrixURIs.html) which involves optional arguments appearing in the URL separated by semicolons.

ParamMaps have been introduced in Angular 4.0, and they support Matrix Params. Thus, the solution becomes simple:

```js
this.router.paramMap.subscribe((params) => {
  // do something with optional and required
})
```

Not just that, the [ParamMap](https://angular.io/api/router/ParamMap) class provides nice getter methods to make it easier to access single, or multiple values. **At the time of writing this post, Router.paramMap does NOT support GET params.**

Hope this helps someone looking for optional arguments in Angular routes.
