Using YUP for validations
=========================

Writing your own validation methods can be cumbersome and can greatly increase the time required to get a validated form up and running. Additionally, you have to keep in mind all of the considerations that a single validation method could have. Let’s look at our `required` method as an example.

**📃 ComponentsForm.vue**

    const required = value => {
      const requiredMessage = 'This field is required'
      if (value === undefined || value === null) return requiredMessage
      if (!String(value).length) return requiredMessage
    
      return true
    }
    

At first glance it would seem that this function is doing a very good job, but now compare it to this other function that does the same job.

    const required = value => {
      if (Array.isArray(value)) return !!value.length
      if (value === undefined || value === null) {
        return false
      }
    
      if (value === false) {
        return true
      }
    
      if (value instanceof Date) {
        return !isNaN(value.getTime())
      }
    
      if (typeof value === 'object') {
        for (let _ in value) return true
        return false
      }
    
      return !!String(value).length
    }
    

It seems like I have failed to think about quite a few edge cases! This is common when trying to “reinvent the wheel” with validations, which is why vee-validate recommends as a best practice to use the library Yup [https://github.com/jquense/yup](https://github.com/jquense/yup) in order to create our validation schemas.

Let’s check this library out.

* * *

Installing Yup
--------------

To start using `yup` we have to first install the library into our project. We can do that by running the following command in our console.

    npm install yup
    
    // OR
    
    yarn add yup
    

After its done installing the packages, we can go back to the `ComponentsForm.vue` and add the import statement next to the `vee-validate` import.

**📃 ComponentsForm.vue**

    import * as yup from 'yup'
    

* * *

Rewriting the validationSchema
------------------------------

Go ahead and delete (or comment out) the current implementation of `validationSchema`.

`Yup` has the ability to create both array- and object-based schemas, but `vee-validate` is expecting an object with a property for each one of our form fields, as we learned in earlier lessons.

In order to accomplish this, we are going to call the `object` function within the yup object we imported. Then, we’ll set it up with an empty object that we will fill up with our validations.

**📃 ComponentsForm.vue**

    const validationSchema = yup.object({
          
    })
    

Yup has a very straightforward way of defining the rules that we want for a particular field. You use the `yup` object and append to it as many functions that describe the validations you want for the the field. Let’s start with the `category`.

**📃 ComponentsForm.vue**

    const validationSchema = yup.object({
      category: yup.string().required()
    })
    

We declare the property `category` as we did before for our schema, and then with the `yup` object append the `string()` method (which means we are expecting a string) and `required()` (which means this field is required).

Let’s try it again for `title`, and this time we are going to set a custom message in case there is an error.

**📃 ComponentsForm.vue**

    const validationSchema = yup.object({
      category: yup.string().required(),
      title: yup.string().required('A cool title is required').min(3)
    })
    

This time we append to `yup` both `required` (with a custom message in case it fails), and the validator `min` (with a value of 3). This will make the title field be both required and have a minimum required length of three characters.

Let’s go ahead and fill out the rest of the fields.

**📃 ComponentsForm.vue**

    const validationSchema = yup.object({
      category: yup.string().required(),
      title: yup.string().required('A cool title is required').min(3),
      description: yup.string().required(),
      location: yup.string(),
      pets: yup.number(),
      catering: yup.boolean(),
      music: yup.boolean()
    })
    

Notice that for `location`, `pets`, `catering`, and `music` we are no longer setting them to `undefined` or an empty validation. We’re going to set them to check for the correct data type that we are expecting. This is not mandatory though, and you could continue to use `undefined` for your unvalidated fields.

Now that we are done, we can go back to the browser and hit that **Submit** button to trigger the validations and everything should be working as before, validations are in place and as soon as the submit event fires they show the error messages.

* * *

Cleaning up
-----------

Before we wrap up, I’d like to point out that we are importing the whole `yup` package in our example right now. This will definitely impact our bundle size!

Since we are only using the `object`, `string`, `boolean` and `number` methods of yup, let’s go ahead and use destructuring so that when we build our application, the parts of the library that we are not using can be removed from the bundle and we can save on some bundle size — this is known as tree-shaking.

**📃 ComponentsForm.vue**

    import { object, string, number, boolean } from 'yup'
    
    [...]
    
    const validationSchema = object({
      category: string().required(),
      title: string().required('A cool title is required').min(3),
      description: string().required(),
      location: string(),
      pets: number(),
      catering: boolean(),
      music: boolean()
    })
    

* * *

Wrapping up
-----------

We have only began to scratch the surface of what `yup` offers as a validation method library. I highly recommend you go visit their Github page and scroll through the documentation to give yourself an idea of all the different validation methods that you can use in your own forms.

In our next lesson we will explore an advanced tip: lazy vs aggressive validation, and how to achieve it with vee-validate.

### Lesson Resources

##### Source Code:

*   [Starting Code](https://github.com/Code-Pop/validating-vue3-forms/tree/lesson5/start)
    
*   [Ending Code](https://github.com/Code-Pop/validating-vue3-forms/tree/lesson5/end)
