Submitting bigger forms
=======================

Now that we know how to handle validation on a per-field level, we’re going to take a look at validating a more robust, complex form. I’ve gone ahead and created a form for us to use, so that we can get right into validating it. Please make sure to start with a fresh copy of this lesson’s branch: `L4-start`.

Whenever the form’s submit event is fired (through a user clicking the submit button, or hitting enter, etc.) we want to make sure that `vee-validate` runs all of our validations. If any of our validations fail, we want to display that error to our users. If all of our validations pass, we want to be able to use the form’s data inside our application to send it to the backend or anything else we need.

* * *

The validation schema
---------------------

Let’s navigate into `ComponentsForm.vue` where our form lives. Inside the `setup` method you will notice I’ve created three validation methods: `required`, `minLength` and `anything`. We’ll cover these in a bit.

It’s very common when validating several fields to extract your validation methods so that you don’t have to repeat yourself — even more common would be to use pre-made methods from a validation library, but more on that on a later lesson.

**📃 ComponentsForm.vue**

    const required = value => {
      const requiredMessage = 'This field is required'
      if (value === undefined || value === null) return requiredMessage
      if (!String(value).length) return requiredMessage
    
      return true
    }
    
    const minLength = (number, value) => {
      if (String(value).length < number) return 'Please type at least ' + number + ' characters'
    
      return true
    }
    
    const anything = () => {
      return true
    }
    

Notice that for the `required` method we first check to see if the value could possibly be `undefined` or `null`, if it is then we return the error message. If its not, then we check that the length of the string is not 0, if it is then we have an empty string and once again must return the error message. Only when both of these checks pass can we then return `true` and tell vee-validate that this field is valid.

Because we have these methods predefined, we can now assign the validation method that we want to each of the fields of our form. At the same time, we can create our `validationSchema` like we learned in the last lesson.

**📃 ComponentsForm.vue**

    const validationSchema = {
      category: required,
      title: value => {
        // TODO
      },
      description: required,
      location: undefined,
      pets: anything,
      catering: anything,
      music: anything
    }
    

There’s two important things to keep in mind here.

First, our `required` method’s first (and only) parameter is our `value`. Remember from the last lesson that `vee-validate` will inject the field’s current value into our validation methods.

Second, you may have noticed that I created a validation method called `anything` that basically doesn’t do anything other than returning `true`. In our current form we have several fields that we don’t want to validate: `location`, `pets`, `catering`, and `music`.

However, we do want `vee-validate` to “know” about them as part of the form’s schema because we want to be able to send them out later on when we submit out form. Trust me for now when I tell you: you want to have all your fields declared on the schema in _most_ cases!

We have two options here. We can either declare a field as `undefined` or we can have an empty validation method like `anything`. I personally prefer `anything` because it’s clearer, but feel free to use either.

For the `title`, we are going to look at how to assign two different validations — since you may want to perform several checks for each field. For the sake of this example, we are going to check that the `title` is both `required` and also has a minimum length of at least 3.

**📃 ComponentsForm.vue**

    title: value => {
      const req = required(value)
      if (req !== true) return req
    
      const min = minLength(3, value)
      if (min !== true) return min
    
      return true
    }
    

We know that our validations return either a string (when they are invalid) or true when they are valid —so we first run our validations against the current `value` and capture them in variables.

Next, we check to see if the `required` validator is successful. If not, we return its error message. Then, we use our `minLength` function to check that the minimum length is at least 3 and once again return the error if its present.

If both our validations pass, then we return `true` to tell `vee-validate` that the field is valid.

Finally, let’s make use of `useForm` to tell `vee-validate` about our validation schema like we learned in the previous lesson.

**📃 ComponentsForm.vue**

    useForm({
      validationSchema
    })
    

* * *

Binding the template
--------------------

Now that we have the `validationSchema` ready, we need to bind all of our fields to the template. For demonstration purposes, let’s do it as we did before with the login form by using `useFields`. Don’t worry, you can just copy paste this incredibly verbose code.

I’ve added `v-model` bindings to every element in the template, along with their `error` binding, and created a `useField` call for each one of them in the `setup`. Finally, I’ve added a `return` object from the `setup` method with all of the fields’ values and error message bindings.

**📃 ComponentsForm.vue**

    <template>
      <div>
        <h1>Create an Event</h1>
        <form @submit.prevent="submit">
          <BaseSelect
            label="Select a category"
            :options="categories"
            v-model="category"
            :error="categoryError"
          />
    
          <h3>Name & describe your event</h3>
          <BaseInput
            label="Title"
            type="text"
            v-model="title"
            :error="titleError"
          />
    
          <BaseInput
            label="Description"
            type="text"
            v-model="description"
            :error="descriptionError"
          />
    
          <h3>Where is your event?</h3>
          <BaseInput
            label="Location"
            type="text"
            v-model="location"
            :error="locationError"
          />
    
          <h3>Are pets allowed?</h3>
          <BaseRadioGroup
            name="pets"
            :options="[
              { value: 1, label: 'Yes' },
              { value: 0, label: 'No' }
            ]"
            v-model="pets"
            :error="petsError"
          />
    
          <h3>Extras</h3>
          <div>
            <BaseCheckbox
              label="Catering"
              v-model="catering"
              :error="cateringError"
            />
          </div>
    
          <div>
            <BaseCheckbox
              label="Live music"
              v-model="music"
              :error="musicError"
            />
          </div>
    
          <div>
            <BaseButton
              type="submit"
              class="-fill-gradient"
              something="else"
            >
              Submit
            </BaseButton>
          </div>
        </form>
      </div>
    </template>
    

**📃 ComponentsForm.vue**

    const { value: category, errorMessage: categoryError } = useField('category')
    const { value: title, errorMessage: titleError } = useField('title')
    const { value: description, errorMessage: descriptionError } = useField('description')
    const { value: location, errorMessage: locationError } = useField('location')
    const { value: pets, errorMessage: petsError } = useField('pets', undefined, { initialValue: 1 })
    const { value: catering, errorMessage: cateringError } = useField('catering', undefined, { initialValue: false })
    const { value: music, errorMessage: musicError } = useField('music', undefined, { initialValue: false })
    
    return {
      category,
      categoryError,
      title,
      titleError,
      description,
      descriptionError,
      location,
      locationError,
      pets,
      petsError,
      catering,
      cateringError,
      music,
      musicError
    }
    

Whew. I think this really puts into perspective what I was pointing out in the last lesson. The `useField` approach can be really repetitive and cumbersome to write.

One thing I want to point out is that some of the fields like `pets` have extra parameters on them that we haven’t learned about yet.

`useField('pets', undefined, { initialValue: 1 })`

The first parameter `pets` is the string that we learned about in the last lesson. It tells the `useField` method which property of our validation schema we want to use.

The second parameter, which is currently `undefined`, is the validation method. We don’t need to use this param because we already set our validation methods on the `validationSchema` we created earlier.

The third param is a configuration object. Amongst what we can set here is the property `initialValue`, which lets us set the initial value for our field. I’ve added an initial value to our checkboxes and radio inputs so that they have an initial value on our state.

* * *

Submitting the form
-------------------

Before we keep going with optimizing our code, I want to take a look at one of the most important parts of having a form and validating it: the part where the user submits the form to us.

Vee-validate provides a method called `handleSubmit` that handles checking that the form is valid before submitting for us. In order to get this method, we need to extract it out of the `useForm` composition function that we use to inject our `validationSchema`.

**📃 ComponentsForm.vue**

    const { handleSubmit } = useForm({
      validationSchema
    })
    

Now that we have `handleSubmit`, we can use it to set up our submit form logic. The `handleSubmit` method has one parameter, a callback function that will be executed if the user tries to submit a _valid_ form. It will _not_ be executed if any errors are present in our form, so we can be certain that whatever handler we place in here will contain valid data.

**📃 ComponentsForm.vue**

    const { handleSubmit } = useForm({
      validationSchema
    })
    
    const submit = handleSubmit(values => {
      console.log('submit', values)
    })
    
    return {
       ...
       submit
    }
    

Notice that the callback function that we are passing into `handleSubmit` will get a `values` parameter injected into it. This `values` is an Object that will contain all the property/value pairs of the form’s data, matching the exact structure that we declared on our `validationsSchema`.

Before we can check the output from the console log though, we have to first `return` the `submit` function that the `handleSubmit` method generates so that we can bind it to our `submit` event on the template.

**📃 ComponentsForm.vue**

    <form @submit="submit">
    

Please note that we have purposely left out the `prevent` modifier from the submit event here, because Vee-Validate will do this for us behind the scenes.

Now that everything is set up, let’s go back to the browser. Notice that if we try to submit the form right away, our validations are all triggered - this is a side effect that we get for “free” when using the `handleSubmit` method.

Not only does it protect our callback from only being triggered once the form is completely valid, but it also triggers all the validations on our form whenever the submit event happens on our form — even on fields that the user has not yet modified.

The following image shows the output of the `console.log` with some dummy data whenever the valid form is submitted.

![https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2Fvalues%20object.png?alt=media&token=f1d6c55c-b6cd-4487-b6e2-dbaa3b19af54](https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2Fvalues%20object.png?alt=media&token=f1d6c55c-b6cd-4487-b6e2-dbaa3b19af54)

* * *

Removing some verboseness
-------------------------

Our code at this point works, but its still looking a bit verbose. I want to show you an alternative way of writing this, which allows us to write fewer lines of code.

The `useForm` composition function has yet another object we can extract that will help us reduce our code, the `errors` object.

**📃 ComponentsForm.vue**

    const { handleSubmit, errors } = useForm({
      validationSchema
    })
    

The `errors` object will neatly contain all of our error messages whenever they are present. So if the `title` input has an error for example, then `errors.title` will contain our error message.

This allows us to first remove a lot of code for the `useField` calls by removing the errors part. We can then simply return `errors` from our `setup` function.

**📃 ComponentsForm.vue**

    [...]
    
    const { value: category } = useField('category')
    const { value: title } = useField('title')
    const { value: description } = useField('description')
    const { value: location } = useField('location')
    const { value: pets } = useField('pets', undefined, { initialValue: 1 })
    const { value: catering } = useField('catering', undefined, { initialValue: false })
    const { value: music } = useField('music', undefined, { initialValue: false })
    
    [...]
    
    return {
      category,
      title,
      description,
      location,
      pets,
      catering,
      music,
      submit,
      errors
    }
    

That’s already quite a bit of code we don’t need to write! The only other change we need to make is in the template, where instead of binding the `error` property of each of our inputs to our old variables, we can now bind it to the corresponding property inside the `errors` object.

The error for title, `titleError` will instead be `errors.title` for example.

**📃 ComponentsForm.vue**

    <template>
      <div>
        <h1>Create an Event</h1>
        <form @submit.prevent="submit">
          <BaseSelect
            label="Select a category"
            :options="categories"
            v-model="category"
            :error="errors.category"
          />
    
          <h3>Name & describe your event</h3>
          <BaseInput
            label="Title"
            type="text"
            v-model="title"
            :error="errors.title"
          />
    
          <BaseInput
            label="Description"
            type="text"
            v-model="description"
            :error="errors.description"
          />
    
          <h3>Where is your event?</h3>
          <BaseInput
            label="Location"
            type="text"
            v-model="location"
            :error="errors.location"
          />
    
          <h3>Are pets allowed?</h3>
          <BaseRadioGroup
            name="pets"
            :options="[
              { value: 1, label: 'Yes' },
              { value: 0, label: 'No' }
            ]"
            v-model="pets"
            :error="errors.pets"
          />
    
          <h3>Extras</h3>
          <div>
            <BaseCheckbox
              label="Catering"
              v-model="catering"
              :error="errors.catering"
            />
          </div>
    
          <div>
            <BaseCheckbox
              label="Live music"
              v-model="music"
              :error="errors.music"
            />
          </div>
    
          <div>
            <BaseButton
              type="submit"
              class="-fill-gradient"
              something="else"
            >
              Submit
            </BaseButton>
          </div>
        </form>
      </div>
    </template>
    

* * *

The initial form values
-----------------------

One more little change I want to make to tidy things up, is to move the _initial values_ of our from from the `useField` methods to the `useForm` method, so that we can neatly wrap them up also in one single spot.

The first thing we have to do is set up our initial form values directly in our `useForm` method’s configuration object, like follows:

**📃 ComponentsForm.vue**

    const { handleSubmit, errors } = useForm({
      validationSchema,
      initialValues: {
        pets: 1,
        catering: false,
        music: false
      }
    })
    

Now that they are all declared here (and in a much neater way), we can remove them from the `useField` methods.

**📃 ComponentsForm.vue**

    const { value: pets } = useField('pets')
    const { value: catering } = useField('catering')
    const { value: music } = useField('music')
    

* * *

Wrapping up
-----------

Now that our form is ready and being validated, let’s take a look at how we can save some development time and leverage some pre-made validation methods with YUP in the next lesson. See you there!

### Lesson Resources

##### Source Code:

*   [Starting Code](https://github.com/Code-Pop/validating-vue3-forms/tree/lesson4/start)
    
*   [Ending Code](https://github.com/Code-Pop/validating-vue3-forms/tree/lesson4/end)
