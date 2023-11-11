Lazy Validation
===============

A common requirement in forms is to have the ability to only trigger the validation of a certain field whenever the user is done typing, this is called lazy validation.

For this lesson we are going to be using our login form as an example, as one of the most used cases for lazy validation are email address fields. It can be quite annoying for the user when they begin to type their email and the application is already complaining that what they typed is not a valid email address.

Text and email input fields fire different events depending on how the user interacts with them. The `input` event fires every time the user adds or deletes a character into the field, the `change` event fires once the field loses focus.

In order to provide a better experience for our users when typing their email address, we are going to learn how to validate our `email` field when the `change` event happens, instead of the default `input`.

* * *

Listening to change and not input
---------------------------------

Components that wrap form inputs such as our demo `BaseInput` usually allow for a `v-model` binding to happen by double way binding into the `modelValue` property and listening for the `update:modelValue` event.

Internally, this `update:modelValue` event usually fires whenever the `input` event from the underlying `<input />` element happens.

This is the case for our demo component `BaseInput` — if you want to know more about how we built this component, check out our [Vue 3 Forms course](https://www.vuemastery.com/courses/vue3-forms/) where we built this component from the ground up.

The first thing we are going to do is remove the `v-model` binding, so ahead and delete that from the first `BaseInput` that holds our `email` binding.

**📃 LoginForm.vue**

    <BaseInput
      label="Email"
      type="email"
      :error="emailError"
    />
    

Our component is no longer being bound to our state, so first we have to reestablish the binding of the state into the component through our `modelValue` property.

**📃 LoginForm.vue**

    <BaseInput
      label="Email"
      type="email"
      :error="emailError"
      :modelValue="email"
    />
    

Next we want to now listen to the `change` event of the underlying input of the component, so we are going to add a `change` event listener and bind it to the `handleChange` function. We haven’t created this function yet, but we will get to that right after.

**📃 LoginForm.vue**

    <BaseInput
      label="Email"
      type="email"
      :error="emailError"
      :modelValue="email"
      @change="handleChange"
    />
    

* * *

Handling the change
-------------------

We need to inform vee-validate that a change in our state for the `email` field has occurred whenever the user changes something in the field. Luckily for us we don’t have to write the logic for the `handleChange` function ourselves, since vee-validate provides it out of the box out of the `useField` composable.

Let’s extract out of the `useField('email')` composition function another property, `handleChange` which is a function. This function will handle all the required logic for vee-validate to update the state internally once its called with the new value - which is exactly what our `@change="handleChange"` binding will do.

Finally, we will return the `handleChange` function we just extracted so that it is available to our template.

    const { value: email, errorMessage: emailError, handleChange } = useField('email')
    [...]
    
    return {
      [...],
      handleChange
    }
    

Now that our bindings are set up, we can go back to the browser and check that everything works. If we start typing anything into the email field we can see that the validation is not immediately fired as before, and when we tab into the password field or click outside the form the `change` event is fired and the validation triggers.

* * *

Handling changes in multiple inputs
-----------------------------------

If your form consists of several inputs, the approach of using `handleChange` for each one of them may become cumbersome as we have learned in previous lessons.

In these cases, there is a function named `setFieldValue` that can be extracted from the `useForm` composition function.

    const { setFieldValue } = useForm(...)
    

    setFieldValue('email', 'test@test.com')
    

You may be wondering why you would need to use this magic method instead of directly updating the form’s model. The `setFieldValue` function will trigger the validation rules on the form’s field who was modified, which would not be the case if we modified it directly.

For more information on `setFieldValue` please check out the [official docs.](https://vee-validate.logaretm.com/v4/api/use-form#composable-api)

* * *

Wrapping up
-----------

As we have learned lazy validation is a very important tool when building forms. When used in conjunction with fields like email or username it provides a polished user experience with a minimal change in our code’s workflow.

### Lesson Resources

##### Source Code:

*   [Starting Code](https://github.com/Code-Pop/validating-vue3-forms/tree/lesson6/start)
    
*   [Ending Code](https://github.com/Code-Pop/validating-vue3-forms/tree/lesson6/end)
