---
title: 'Enabling inferred rules in VeeValidate 2.1.4'
date: Sun, 23 Dec 2018 16:33:49 +0000
draft: false
tags: ['vee-validate', 'Vue.js', 'vuejs']
---

If upgrading to VeeValidate 2.1.4 you will find that any of your form validation making use of `inferred` rules will be broken. This is because the functionality is now disabled by default.

To enable it again, you will need to set the `validity` flag to `true` when you install the plugin. This is shown below:

```
Vue.use(VeeValidate, {
  validity: true
});
```

Fortunately, as you can see there is not much needed to get inferred rules working again.