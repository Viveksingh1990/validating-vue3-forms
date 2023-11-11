Validating at form level
========================

Now that we know how to validate a single input field in our forms, let’s take it a step further and learn how to set up our validations at the form level. This will allows us to define rules for our whole form at once without so much code “clutter”.

* * *

Validating the password field
-----------------------------

If we were to continue with the current strategy, in order to validate our password field we would now add a new `useField` call for it. In a form with two fields like this login form, this may not seem like a big issue since its only a couple more lines of code. But when we are dealing with substantially bigger forms, like a registration form for example, this can quickly become hard to manage.

Luckily there is a very similar way to declare how we want our forms to be validated all at once: the `useForm` function.

Let’s first add to our `import` statement on the top of `LoginForm.vue` the `useField` and `useForm` methods — they are both imported from the `vee-validate` package.

**📃 LoginForm.vue**

    import { useField, useForm } from 'vee-validate'
    

When validating at form level, we want to create an object that will represent the structure of our form’s model, or data — this is usually referred to as the _Schema_.

We know that we have two things we have to capture from our user here, the `email` and `password`, so let’s start by creating an object with these properties inside the `setup` function.

**📃 LoginForm.vue**

    const validations = {
      email: value => {
    
      },
      password: value => {
    
      }
    }
    

Notice that each of our properties point to a function, this function is what `vee-validate` will use when validating each of our inputs.

Notice also that the function receives a single param: `value`. This `value` contains the data that the user is entering into our form inputs.

We already have a function to validate our email that we wrote on the previous lesson, so let’s cut it out of the `useField` call we had for our email input and paste it into our `validations` object’s `email` property instead.

**📃 LoginForm.vue**

    const validations = {
      email: value => {
        if (!value) return 'This field is required'
    
        const regex = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
        if (!regex.test(String(value).toLowerCase())) {
          return 'Please enter a valid email address'
        }
    
        return true
      },
      password: value => {
    
      }
    }
    
    const { value: email, errorMessage: emailError } = useField('email')
    

Notice that our `useField` call remains, but only with the first parameter `'email'`. Be careful, this string `'email'` must match the `email` property of our validation’s schema. If the property inside of our `validations` object was called `emailAddress` for example, our `useField` call here would have the string `'emailAddress'` instead to reflect it.

Now that we’ve refactored everything, let’s add the actual validation method for our `password` field. We will simply check that the `value` exists and that it has a length greater than 0 — commonly known as `required`.

**📃 LoginForm.vue**

    password: value => {
      const requiredMessage = 'This field is required'
      if (value === undefined || value === null) return requiredMessage
      if (!String(value).length) return requiredMessage
    
      return true
    }
    

* * *

Hooking up our form to the ‘validationSchema’
---------------------------------------------

Now that our validations are ready, we have to tell `vee-validate` that we want it to use this object as the validation’s schema. We can do this with the `useForm` method we imported earlier.

The `useForm` method takes an object as its only parameter. Within this object we can define several configuration properties, but the one we are interested in right now is called `validationSchema`.

Let’s set this property to our `validations` object.

**📃 LoginForm.vue**

    const validations = {
      email: value => {
        if (!value) return 'This field is required'
    
        const regex = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
        if (!regex.test(String(value).toLowerCase())) {
          return 'Please enter a valid email address'
        }
    
        return true
      },
      password: value => {
        const requiredMessage = 'This field is required'
        if (value === undefined || value === null) return requiredMessage
        if (!String(value).length) return requiredMessage
    
        return true
      }
    }
    
    useForm({
      validationSchema: validations
    })
    

Now that `vee-validate` knows how to validate our form’s data, let’s make sure that we are correctly binding the `v-model` for the password, as well as getting any possible errors.

Just like we did for our email input, we are going to use the `useField` method to get them both and return them from our `setup` method so that we can use them on our template.

**📃 LoginForm.vue**

    const { value: email, errorMessage: emailError } = useField('email')
    const { value: password, errorMessage: passwordError } = useField('password')
    
    return {
      onSubmit,
      email,
      emailError,
      password,
      passwordError
    }
    

Notice that we are using JavaScript object destructuring one more time to extract the `value` and `errorMessage` out of the password field object, and renaming them to `password` and `passwordError` respectively, so that we can tell them apart.

Let’s now go to the `template` and set up the `v-model` and `error` bindings for the password input.

**📃 LoginForm.vue**

    <BaseInput
      label="Password"
      type="password"
      v-model="password"
      :error="passwordError"
    />
    

* * *

Wrapping up
-----------

Fire up your browser and start typing into both your email and password fields. Notice how as you type the first character, the validation triggers and the methods that we added for each one respectively calculate if the fields are valid or not.

![https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2Finvalid_form.opt.jpg?alt=media&token=cbdbc196-aa2c-4291-88a8-1327c5fb55a2](https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2Finvalid_form.opt.jpg?alt=media&token=cbdbc196-aa2c-4291-88a8-1327c5fb55a2)

Now that our form’s validation logic is centralized, let’s learn how to also centralize our error messages using `useForm` so that we don’t have to call `useField` manually for every single field we want to add to our form. We’ll handle that in the next lesson. See you there!

### Lesson Resources

##### Source Code:

*   [Starting Code](https://github.com/Code-Pop/validating-vue3-forms/tree/lesson3/start)
    
*   [Ending Code](https://github.com/Code-Pop/validating-vue3-forms/tree/lesson3/end)
