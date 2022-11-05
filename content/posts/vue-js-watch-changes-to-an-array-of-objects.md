---
title: 'Vue.js watch changes to an array of objects'
date: Sun, 24 Jun 2018 18:21:30 +0000
draft: false
tags: ['Vue.js', 'vuejs']
---

Vue.js provides the watch option for responding to changes in data. By default, when working with an array of objects, a `watch` object's handler function is only triggered by mutations to the array itself and not the values of the object properties. Turning to the docs, in order to detect nested value changes in objects you need to set the watch object's `deep` property to `true` as shown below:```
vm.$watch('todos', callback, {
  deep: true
});
```This is Illustrated with [this](https://codepen.io/amuponda/pen/pKKZeQ?editors=1111) simple codepen example.  With the `deep` property set to `false`:```
watch: {
  todos: {
    handler: function(newTodos) {
      console.log(this.deep)
      this.formattedTodos = \[\]
      for(var i = 0; i < newTodos.length; i++) {
        this.formattedTodos.push({name: newTodos\[i\].name})
      }
    },
    deep: false //set to true to enable deep watch
  }
}
```The formatted array created in the handler function only updates when you add items to the list. Setting `deep: true` will also trigger the `watch` callback on changes to the nested objects.