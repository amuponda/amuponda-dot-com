---
title: 'Working with objects as select options in Vue.js'
date: Wed, 12 Dec 2018 23:57:26 +0000
draft: false
tags: ['Vue.js', 'vuejs']
---

Using objects as `select` options in Vue.js is straightforward. Together with the `v-for` directive you create the options as follows:

```
<select v-model="selected">
 <option v-for="acc in accounts" :key="acc.id" :value="acc">{{acc.name}}</option>
</select>
```

Binding the `value` attribute to the object itself and the option's text to a key in the object. You can use [VeeValidate](https://baianat.github.io/vee-validate/) on `select` as well:

```
<select v-model="selected" required v-validate></select>
```

Adding a noSelection option
---------------------------

A default (noSelection) option can be added by having an `option` bound to `null` in the option list.

```
<select v-model="selected" name="account" required v-validate>
<option :value="null">Choose account</option>
 <option v-for="acc in accounts" :key="acc.id" :value="acc">{{acc.name}}</option>
</select>
```

Of course the Vue instance variable selected has to be initialized as `null`.

```
new Vue({
  el: '...',
  data: {
    selected: null,
    accounts: \[
      ...
    \]
  }
})
```

You can have a look at this [codepen](https://codepen.io/amuponda/pen/jXNxMW?editors=1111) for an illustration.