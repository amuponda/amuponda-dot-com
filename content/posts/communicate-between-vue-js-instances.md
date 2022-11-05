---
title: 'Communicate between Vue.js instances'
date: Wed, 02 Jan 2019 16:54:04 +0000
draft: false
tags: ['Vue.js', 'vuejs']
---

To communicate between two Vue instances you need to create an event hub/event bus. Since Vue instances implement an event emitter interface this can easily be achieved by declaring an empty Vue instance:

```
let eventHub = new Vue()
```

You can then `$emit` events from the first instance as follows:

```
let appOne = new Vue({
 methods: {
  onClick () {
   eventHub.$emit('button-clicked')  
  }
 }
})
```

And use the `created` hook in the other app to listen for the event:

```
let appTwo = new Vue({
 methods: {
  showAlert () {
   alert('Button clicked')
  }
 },
 created () {
  eventHub.$on('button-clicked', this.showAlert)
 },
 beforeDestroy () {
  eventHub.$off('button-clicked', this.showAlert)
 }
})
```

Remember you can also use the events interfaces' `$once` method to listen to events only once. As always, an illustrative example is available in [this](https://codepen.io/amuponda/pen/EGoEXW) codepen.