---
title: 'Making a field conditionally required with VeeValidate'
date: Sun, 16 Dec 2018 16:36:46 +0000
draft: false
tags: ['vee-validate', 'Vue.js', 'vuejs']
---

There are instances when you are using VeeValidate for form validation and you want a field to be required only if a certain condition is met. This blog post will illustrate how to achieve that in two different ways.

1\. Using a computed property
-----------------------------

You can make use of a computed properties to return a dynamic validation expression.```
<input name="email" v-validate="rules" />
```For the input element above the computed property `rules` would look something like this:```
computed: {
  rules () {
    let rulesExp = "email"
    return rulesExp += this.isRequired ? "|required" : ""
  }
}
```The function can be made cleaner by making use of [inferred rules](https://baianat.github.io/vee-validate/guide/inferred-rules.html#demo) for the non dynamic validation rules:```
<input type="email" name="email" v-validate="rules" />
```And just returning the dynamic rules from the computed property:```
computed: {
  rules () {
    return this.isRequired ? "required" : ""
  }
}
```

2\. Using the object syntax for rules
-------------------------------------

Rules in VeeValidate can be expressed either as a validation expression or an object:```
<!-- either -->
<input v-validate="'email|required|max'" />
<!-- or -->
<input v-validate="{ required: true, email: true, max:50 }" />
```In order to conditionally require the field you would reactively bind the required attribute of the object like so:```
<input v-validate="{ required: x, email: true, max:50 }" />
```_where **x** is either a computed property or an expression evaluating to true or false_ And that is that, two sure fire ways to conditionally/reactively make form fields required. You can have a look at [this](https://codepen.io/amuponda/pen/jQoPGg?editors=1111) codepen for an example making use of the two techniques.