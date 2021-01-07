---
layout: post
title:      "USE CONSTANTS TO SET BACKEND URL FOR BOTH PRODUCTION AND DEVELOPMENT :"
date:       2021-01-07 21:53:20 +0000
permalink:  use_constants_to_set_backend_url_for_both_production_and_development
---

Could also help with setting other constants for any responsibility. 
#### TECHNOLOGIES USED FOR THIS EXAMPLE: REACT, JAVASCRIPT, CREATE-REACT-APP

### WHY?

For my final project with Flatiron School in NYC, I wanted to deploy my project to Heroku since in the past I wasn't successful. The only problem I found with deployment was the url for backend calls needed to change every time I committed. Which as you can guess... was a pain. 

So I found a solution to never have to worry about that and subsequently learned other uses for it. 

### Constants.js

Very simple you'll want to create a JavaScript file in the root of your `src` directory that will handle the logic. 

`touch src/Constants.js`

Now add the following code: 
*DISCLAIMER: the following is generic... consider what you need the backend api calls to be then set the urls accordingly. i.e. my-heroku-app can be whatever your name is, and it doesn't need to use the "/api/v1/" that is just how my backend is structured.
Moreover, the name of the constants and the urls is completely discretionary. i.e. `const prod = ...` can be `const banana = ... ` and still serve the same purpose*

```
const prod = {
	url: {
		BASE_URL: 'https://my-heroku-app.herokuapp.com/api/v1/',
		AUTH_URL: 'https://my-heroku-app.herokuapp.com/',
	},
};

const dev = {
	url: {
		BASE_URL: 'http://localhost:3090/api/v1/',
		AUTH_URL: 'http://localhost:3090/',
	},
};

export const config = process.env.NODE_ENV === 'development' ? dev : prod;
```

*The only thing that SHOULD stay the same for your code is the last line... let's talk about why*

## Let's break it down:
### `const prod and const dev`
At a high level, this is an obvious naming choice for the `const` variables. One is for when the project is in production, the other is for development. As stated before, they can be called whatever you want, but ensure that you understand which is for development and which is for production.

### `{ url: { BASE_URL: '...', AUTH_URL: '...,'}}`
This is the object returned by each variable. In this example I have two variations of the url because I wanted one for backend api calls on my database, and the other for authorization (which is not nested in the namespace of 'api/v1/').
This is also discretionary, because both aren't required, but even **ONE** should be nested in with a 'url' key. So, regardless the object should look like

```
{url: {
my consts
}}
```
The reasons why will be clear soon. 

### `export const config = process.env.NODE_ENV === 'development' ? dev : prod;`
This is the 'Golden Ticket'. This isn't inherently magical. What you see is an `export` statement for a new `const` named `config` that equals a conditional statement. 

## `process.env.NODE_ENV`
Let's take a look at this in pieces: 
### `process.env`
`process.env` is a:

> global variable is injected by the Node at runtime for your application to use and it represents the state of the system environment your application is in when it starts... - Joseph Matthias Goh, Codeburst.io, 2017
> 

Ok, so it's always accessible, where does the `NODE_ENV` come from?
### `NODE_ENV`

Firstly, you have to know that when using [**CREATE-REACT-APP**](https://reactjs.org/docs/create-a-new-react-app.html) the `NODE_ENV` variables for both production and development are automatically injected. 

> When developing web applications by using Create React App, developers get NODE_ENV=development on their local environment and NODE_ENV=production on the production build by default. And, modifying NODE_ENV is forbidden. - Donghyuk (Jacob) Jang, Dev.to, 2019
> 

This means that you don't have to worry about setting these manually, however there are resources to show you how and I will link them below. 

This variable is important because if it's not set, this method won't work. 

### Lastly: `... === 'development' ? dev : prod;`

This is the magic... sort of. This is asking the `process.env.NODE_ENV` if its value is 'development'. This is why I said it's important to note which variable is for development and which is for production. If true, the conditional returns the 'dev' constant and if false, the conditional returns the 'prod' constant. 

## NOW LET’S USE IT! 
You don't need these constants to be available throughout the entire app. While it may seem logical to import in your `index.js` or even your `App.js` or whatever root js file you're working with, it's just not necessary. 

Instead, I want to use this in my actions files because that's where all the fetch requests will happen. 

At the very top of the file you want to use your constants in, type the following code: 

```
import { config } from '../Constants';
const url = config.url.AUTH_URL;
```

Two things are happening: **1**. `import {config} ...` is importing the config constant that we exported from `Constants.js`. **2**. I'm setting a new variable with the variable of my choosing. In this case I want "url" to equal, the url named 'AUTH_URL'. 
The logic in the last line of my `Constants.js` file will determine which 'AUTH_URL' to use based on the results of `process.env.NODE_ENV`!

And that's it. 

## Conclusion

We created a `Constants.js` file, we exported the `config` const based on the `process.env.NODE_ENV` the correct value, and then imported the correct url into the file we want to use it in.

The applications of this process are vast. Creating a single JavaScript file to handle a process you'd otherwise repeat over and over again helps adhere to that single responsibility rule and declutters your code. 

Best application for this specific example is that I now don't have to worry about which url is committed when I push my code to Github or Heroku. It will just know which url to use.  

### Resources: 
[process.env: What it is and why/when/how to use it effectively](https://codeburst.io/process-env-what-it-is-and-why-when-how-to-use-it-effectively-505d0b2831e7)

[Managing .env variables for provisional builds with Create React App](https://dev.to/jam3/managing-env-variables-for-provisional-builds-h37#:~:text=When%20developing%20web%20applications%20by,And%2C%20modifying%20NODE_ENV%20is%20forbidden.)

[CREATE-REACT-APP](https://reactjs.org/docs/create-a-new-react-app.html)


