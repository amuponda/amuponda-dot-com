---
title: 'Vuejs using MDBootstrap''s custom input with Vee-validate'
date: Mon, 09 Jul 2018 18:59:50 +0000
draft: false
tags: ['mdbootstrap', 'vee-validate', 'Vue.js', 'vuejs']
---

The [vee-validate](https://baianat.github.io/vee-validate/guide/getting-started.html#installation)  directive is used as follows: \[html\] <input v-validate="'required|email'" name="email" type="text" v-model="email"> <p v-show="errors.has('email')" class="text-danger"> <small>{{ errors.first('email) }}</small> </p> \[/html\] Essentially, vee-validate creates an array of validation errors via the [ErrorBag](https://baianat.github.io/vee-validate/api/errorbag.html#api) wrapper class and you can access that array in your template and javascript. MDBoostrap provides a custom input component, and according to the [docs](https://vuejs.org/v2/guide/components.html#Using-v-model-on-Components) , for it to work with v-model, the <input> inside the component should:

*   Bind the `value` attribute to a `value` prop - (check) _you can pass in a value prop_
*   On `input`, emit its own custom `input` event with the new value - (check) _it has a custom event called 'input' fired whenever the @change and @input events occur on the nested input tag_

So combining the two you can change the html to this: \[html\] <md-input v-validate="'required|email'" name="email" label="Email" icon="envelope" :value="email" @input="email = $event" /> <p v-show="errors.has('email')" class="text-danger"> <small>{{ errors.first('email') }}</small> </p> \[/html\] The md-input component has`value`, `errorMsg`and`successMsg` props which I assumed could be used to display validation errors but at close inspection this is not the case. In the current version (v1.0.0), the props are not used in any meaningful way. So the best you can do to pass the validation error message to the component is by making use of the slot element. So the final result would be: \[html\] <md-input v-validate="' required|email'" name="email" label="Email" icon="envelope" :value="email" @input="email = $event"> <p v-show="errors.has('email')" class="text-danger"> <small>{{ errors.first('email') }}</small> </p> </md-input> \[/html\] You can  checkout the example code on  [github.](https://github.com/amuponda/blog-posts/tree/master/mdbbootstrap-vee-validate)