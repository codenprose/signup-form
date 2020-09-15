# Building and Validating Beautiful Forms with Vanilla HTML, CSS & JS

Created By: Daniel Hunter
Last Edited: Sep 15, 2020 12:05 PM
Tags: Nolibs

Forms are hard.  Knowing how to properly collect and validate user data is one of the most important skills a frontend developer needs to have.  Edge cases abound. You have to consider all of the ways a user could break your form while simulataneously providing an excellent user experience.  

The UX piece is important because forms are the gatekeepers to conversions for products and services.  If you, frontend developer, get it wrong, there could be significant financial consequences.

This is why there are thousands (slight exageration) of form libraries out there that implement industry best practices.  There is nothing wrong with using these libraries, the problem is using them without understanding how forms actually work and why certain patterns are considered *standards*.

I'm going to show you how I would build a signup form using only HTML, CSS, and JavaScript. 

Ok, without further ado, let's dive in.

## The single (default) state trap

![Building%20and%20Validating%20Beautiful%20Forms%20with%20Vanil%20349e43e2f425449fbb45691a3fb511ac/sign-up-form-desktop.png](Building%20and%20Validating%20Beautiful%20Forms%20with%20Vanil%20349e43e2f425449fbb45691a3fb511ac/sign-up-form-desktop.png)

When you get a design file like this, your first question should be, how many states aren't represented here?  When planning your work, make sure to consider what isn't in the design that must be accounted for.  You need to carefully review the feature requirements and ask questions if you think something is missing.

## Feature Requirements

Putting my product mangager hat on.

- The user must provide an email address
- Password must be at least 10 characters long and contain at least one uppercase letter, number, and special character.
- We should show error messages to the user when they don't meet the requirements

## Markup

The first code we'll write will be HTML with a just a dash of CSS.  After this step, this is what we'll have:

**Desktop**

![Building%20and%20Validating%20Beautiful%20Forms%20with%20Vanil%20349e43e2f425449fbb45691a3fb511ac/sign-up-form-bare-bones.png](Building%20and%20Validating%20Beautiful%20Forms%20with%20Vanil%20349e43e2f425449fbb45691a3fb511ac/sign-up-form-bare-bones.png)

**Mobile**

<iframe src="[https://nolibs.io/dkh/motifs/WhCWybHQ/embed](https://nolibs.io/dkh/motifs/WhCWybHQ/embed)" frameborder="0" width="100%" height="450" title="Sign Up Form - Markup" allowtransparency="true" allowfullscreen="allowfullscreen" class="motif-embed" style="border: 1px solid lightgray; border-radius: 4px;"></iframe>

Doesn't seem like much yet but there's some good work here.  Let's dive in a bit.

- We've setup aside and main elements along with our form
- I'm using BEM as a guide for creating class names and semantic html elements for readability.
- Our signup up page takes a mobile first approach, meaning, we write mobile styles first and add breakpoints for desktop styles.
- I'm leveraging CSS grid for the overall layout and flexbox for position elements in the main section.
- I've added a submit event listener for the form along with an event handler function that simply logs the event object for now.

## Validation

Let's leverage some built in validation logic by choosing our input types wisely. We'll use the following:

- Email input type
- Password input type

The email input type gives us a couple of nice validations for free.

1. It checks to make sure the `@` symbol is used 
2. Checks that there's text after the symbol as well

However, the built in validation doesn't check to see if there's a domain extension e.g. `.com`.  The only way to be absolutely, positively sure that what the user entered is in fact a valid email is to actually send an email to the address they give you and make them confirm. Other than that it's all just guesses.

Since both Email and Password are required, let's add the `required` attribute to both elements. We'll also add a `minlength` attribute to the password input.

```html
<form id="dkh-signup-form">
  <div class="dkh-form-header">
    <div>
      <small>Sign up with</small>
      <div class="dkh-form-header__social-wrapper">
        <button type="button" class="dkh-btn dkh-btn-icon dkh-btn-github">
          Github
        </button>
        <button type="button" class="dkh-btn dkh-btn-icon dkh-btn-twitter">
          Twitter
        </button>
      </div>
    </div>
  </div>
  <div class="dkh-form-body">
    <small>Or sign in with email and password</small>
    <div class="dkh-form-field">
      <fieldset>
        <input autofocus class="dkh-form-field__input" name="email" type="email" id="email" required placeholder="Email">
      </fieldset>
      <div class="dkh-form-field__messages"></div>
    </div>
    <div class="dkh-form-field">
      <fieldset>
        <input class="dkh-form-field__input" name="password" type="password" id="password" required minlength="10" placeholder="Password">
      </fieldset>
      <div class="dkh-form-field__messages"></div>
    </div>
  </div>
  <div class="dkh-form-footer">
    <button class="dkh-btn dkh-btn-primary" type="submit">Sign Up</button>
  </div>
</form>
```

The `type=email` attribute informs the browser that it should validate the input string as an email.

![Building%20and%20Validating%20Beautiful%20Forms%20with%20Vanil%20349e43e2f425449fbb45691a3fb511ac/email-validation.png](Building%20and%20Validating%20Beautiful%20Forms%20with%20Vanil%20349e43e2f425449fbb45691a3fb511ac/email-validation.png)

The minlength attribute on the password input gives us this helpful error message:

![Building%20and%20Validating%20Beautiful%20Forms%20with%20Vanil%20349e43e2f425449fbb45691a3fb511ac/Screen_Shot_2020-08-15_at_2.20.18_PM.png](Building%20and%20Validating%20Beautiful%20Forms%20with%20Vanil%20349e43e2f425449fbb45691a3fb511ac/Screen_Shot_2020-08-15_at_2.20.18_PM.png)

Now, in our `handleSignupFormSubmit` function, we can use the [FormData API](https://developer.mozilla.org/en-US/docs/Web/API/FormData/Using_FormData_Objects) to get the values from our form, and eventually submit them to an API.

```jsx
function handleSignupFormSubmit(e) {
  // prevent default browser behaviour
  e.preventDefault();

  const formDataEntries = new FormData(signupForm).entries();
  const { email, password } = Object.fromEntries(formDataEntries);

  // submit email and password to an API
}
```

## Error messages

The error messages that are rendered by the browser are helpful to start but what if you want these messages to render below their respective form input?  What if you want to control how they look?

Sadly, the browser doesn't give us any control over how the default error message are rendered.

This is where our `dkh-form-field__messages` div elements come into play.  We can render our custom error messages inside of these elemements and later on we'll add css to style them.

Let's write a couple custom validation functions to check that our user's password and email values meet the requirements. We'll call these validation functions then the user submits the form.

```jsx

function validatePassword(password, minlength) {
  if (!password) return 'Password is required';

  if (password.length < minlength) {
    return `Please enter a password that's at least ${minlength} characters long`;
  }

  const hasCapitalLetter = /[A-Z]/g;
  if (!hasCapitalLetter.test(password)) {
    return 'Please use at least one capital letter.';
  }

  const hasNumber = /\d/g;
  if (!hasNumber.test(password)) {
    return 'Please use at least one number.';
  }

  return '';
}
```

```jsx
function validateEmail(email) {
  const isValidEmail = /^\S+@\S+$/g
  if (!isValidEmail.test(email)) {
    return 'Please enter a valid email';
  }

  return '';
}
```

The regex `/^\S+@\S+$/g` is far from bullet proof but it at least checks to make sure there are characters before and after the `@` symbol.  Again, the best way to validate an email is to send a confirmation email to any user that signs up.  The user would then have to open that email and click a link to confirm their email address is valid. 

If you'd like to dig deeper into client side email validation, this is a great [thread](https://stackoverflow.com/questions/46155/how-to-validate-an-email-address-in-javascript).

Now, let's figure out how to render the error messages to the page.

```jsx
function handleSignupFormSubmit(e) {
  // prevent default browser behaviour
  e.preventDefault();

  const formDataEntries = new FormData(signupForm).entries();
  const { email, password } = Object.fromEntries(formDataEntries);

  const emailErrorMessage = validateEmail(email);
  const passowrdErrorMessage = validatePassword(password);

  if (!emailErrorMessage) {
		// select the email form field message element
    const emailErrorMessageElement = document.querySelector('.email .dkh-form-field__messages');
    // show email error message to user
    emailErrorMessageElement.innerText = emailErrorMessage;
  }

  if (passowrdErrorMessage) {
		// select the email form field message element
    const passwordErrorMessageElement = document.querySelector('.password .dkh-form-field__messages');
    // show password error message to user
    passwordErrorMessageElement.innerText = passowrdErrorMessage;
  }
}
```

One additional thing I'll call out, in order for these messages to show up, we need to remove the `required` attributes from both the email and password inputs. 

We need to change the type attribute value for the email input. 

```html
<input autofocus class="dkh-form-field__input" type="text" name="email" id="email" required placeholder="Email">
```

We also need to change remove the minlength attribute from the password input.  

```jsx
<input class="dkh-form-field__input" name="password" type="password" id="password" required placeholder="Password">
```

Updating these attributes removes the browswer based validation in favor of our own validation logic.

Here's how our custom error messages will render:

![Building%20and%20Validating%20Beautiful%20Forms%20with%20Vanil%20349e43e2f425449fbb45691a3fb511ac/Screen_Shot_2020-09-15_at_11.41.03_AM.png](Building%20and%20Validating%20Beautiful%20Forms%20with%20Vanil%20349e43e2f425449fbb45691a3fb511ac/Screen_Shot_2020-09-15_at_11.41.03_AM.png)

## Styles

The reason I leave css until the end is, in my personal expereince, it's a little harder to focus on logic when the visual design is complete.  When a component or page "looks" done to the eye it can create a false sense that it actuallly is done.  I don't have any research to back this up, just my personal opinion.

Here's the state of our code after adding quite a bit of CSS.

**Desktop**

![Building%20and%20Validating%20Beautiful%20Forms%20with%20Vanil%20349e43e2f425449fbb45691a3fb511ac/sign-up-form-desktop%201.png](Building%20and%20Validating%20Beautiful%20Forms%20with%20Vanil%20349e43e2f425449fbb45691a3fb511ac/sign-up-form-desktop%201.png)

**Mobile**

<iframe src="[https://nolibs.io/dkh/motifs/2fnKSwMn/embed](https://nolibs.io/dkh/motifs/2fnKSwMn/embed)" frameborder="0" width="100%" height="450" title="Sign Up Form" allowtransparency="true" allowfullscreen="allowfullscreen" class="motif-embed" style="border: 1px solid lightgray; border-radius: 4px;"></iframe>

I included [font awesome](https://fontawesome.com/) icons for the Github and Twitter Buttons.

```html
<div class="dkh-form-header">
  <div>
    <small>Sign up with</small>
    <div class="dkh-form-header__social-wrapper">
      <button type="button" class="dkh-btn dkh-btn-icon dkh-btn-github">
        <i class="fab fa-github fa-lg"></i>
        Github
      </button>
      <button type="button" class="dkh-btn dkh-btn-icon dkh-btn-twitter">
        <i class="fab fa-twitter fa-lg"></i>
        Twitter
      </button>
    </div>
  </div>
</div>
```

## Summary

We now have the building blocks to build sign up and log in forms without 3rd party libraries.  If you're using a framework like React or Vue there are so many awesome form and validation libraries that you can lean on to get the job done quickly.  However, if you're new to software development, I would encourage you to focus on the fundamentals first before using these tools.

I got my first job as a developer five years ago and my journey into tech has forever changed my life for the better. I believe that it's important to focus on and master the fundamentals so that you can more easily grasp tools like React and Vue.

One of the problems I noticed when [running a meetup](https://technical.ly/philly/2018/03/05/free-coding-camp-philly-study-hall/) myself for years was that people who were new to coding reached for libraries and frameworks too quickly. This ended up hurting them and many struggled during interviews. 

If you are learning how to code and could use some help, feel free to reach out to me on [twitter](https://twitter.com/danielkhunter). Looking forward to helping however I can.