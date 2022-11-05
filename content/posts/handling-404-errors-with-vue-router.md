---
title: 'Handling 404 errors with Vue Router'
date: Mon, 28 Jan 2019 05:54:05 +0000
draft: false
tags: ['vue-router', 'Vue.js', 'vuejs']
---

Using the asterisk (`*`) in the Vue Router object definitions allows you to match anything, you just have to be careful and place them at the end of your routes array so that precise paths are matched first.

Therefore, to handle 404 errors, all you will have to do is match the catch all path to your 404 error component. The Vue Router object would be defined like so:

```
new Router({
 routes: \[
 	{
 	  path: '/blog-post/:id'
 	  name: 'blogPost',
 	  component: BlogPost
 	},
 	{
 	  // matches everything else	
 	  path: '\*',
 	  name: 'notFound'
 	  component: NotFound
 	}
 \]		
}) 
```

As you can see, it's dead simple. The tricky bit comes when you want to programatically navigate to the 404 page.

### Programatic navigation to 404 page

Suppose you want redirect the user to the 404 page when they request a blog post that does not exist. You could achieve this in your navigation guards as shown below:

```
import router from '../router'
...
// in component guards
beforeRouteEnter (to, from, next) {
  axios.get(\`/api/posts/${to.params.id}\`).then(response => {
    next(vm => {
      vm.post = response.data.post
    })
  })
    .catch(reason => {
      if (reason.response.status === 404) {
         router.push({ name: 'notFound', params: { '0': to.path } })
      } 
    })
},
beforeRouteUpdate (to, from, next) {
  axios.get(\`/api/posts/${to.params.id}\`).then(response => {
    this.post = response.data.post
    next()
  })
    .catch(reason => {
       router.push({ name: 'notFound', params: { '0': to.path } })
    })
}
```

_Because we are redirecting to an `*` the router does not know what path to put in the location, so you have to pass that path in_. You do that by passing the `'0'` parameter in your call to `router.push()`. If you do not do this you will get the error below:

`missing param for named route "notFound": Expected "0" to be defined`

An approach I have seen others take when tackling this issue is to define two routes:

```
\[
  ...  
  { path: '/404', component: NotFound },  
  { path: '\*', redirect: '/404' },  
  ...  
\]
```

And have the the catch all redirect to specific path. What I dislike about this approach is that it does not preserve the offending URL.

This blog post has detailed how to programatically navigate to the 404 page in your Vue.js (2.2+) apps whilst maintaining the URL of the request in question. An example app showing this functionality is available on [github](https://github.com/amuponda/blog-posts/tree/master/vue-page-not-found).