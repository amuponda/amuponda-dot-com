---
title: 'VeeValidate pretty field names error messages in'
date: Tue, 15 Jan 2019 15:55:56 +0000
draft: false
tags: ['vee-validate', 'Vue.js', 'vuejs']
---

By default VeeValidate uses the `name` or `data-vv-name` attributes of the input element for the field in error messages. For the field below:

```
<input name="startDate" v-validate="'required'" type="text"/>
<span>{{ errors.first('startDate') }}</span>

```

The error message when the start date is not specified would be: _The startDate field is required_, which is not exactly user friendly.

Step forward the `data-vv-as` attribute. As the docs put it, it specifies a pretty name for the field. It can be used as follows:

```
<input name="startDate" data-vv-as="start date" v-validate="'required'" type="text"/>
<span>{{ errors.first('startDate') }}</span>
```

The error message would then be: _The start date field is required_ i.e not camelCase representation of the field name.

The `data-vv-as` attribute provides a quick and easy way to achieve user friendly error messaging. Prior to learning this, I had to resort to modifying the internal VeeValidate dictionary:

```
const dictionary = {
 en: {
  attributes: {
   startDate: 'start date'
  }
 }
}
Validator.localize(dictionary)
```

As you can see, this is too verbose given the simplicity of what is achieved. You can find an example to illustrate this [here](https://codepen.io/amuponda/pen/ebxYXg).