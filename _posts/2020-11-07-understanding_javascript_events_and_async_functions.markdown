---
layout: post
title:      "Understanding Javascript Events and Async Functions"
date:       2020-11-08 02:44:54 +0000
permalink:  understanding_javascript_events_and_async_functions
---

CSS is an art form, HTML is very straight forward, and jQuery will make a lot of the Javascript that plagued me seem much easier... maybe even React? To be seen.

However, my Communities App has taught me tons about JS Events and Async Functions. Maybe I can help clear things up. 

```
async function getAllCommunities() {
  const resp = await fetch(`${BASE_URL}communities`);
  const data = await resp.json();
  console.log(data)
}
```

Async functions let working with Promises easier.

> A promise is a placeholder for a value that can either resolve or reject at some time in the future

So why Async? Why Await? Why Promise:

I'm a visual learner, so I'll try my best.

Although you have to start by understanding how Javascript works (and you can learn more here: [the Javascript Engine](https://dev.to/lydiahallie/javascript-visualized-the-javascript-engine-4cdf))

Let me breakdown what this Async function does:

```
async function getAllCommunities() {
  const resp = await fetch(`${BASE_URL}communities`);
...
```

The first part of this is `async function` that declares this function as Asynchronous. 

> Asynchronous code takes statements outside of the main program flow, allowing the code after the asynchronous call to be executed immediately without waiting

```
...
const resp = await fetch...
...
```

`await` in this context is the interesting part. See in order for this async function to make JS asynchronous, you'll want to allow the function to pause and allow something else to happen. 
BUT WHY?

Because this way you allow other tasks to happen without stopping JS. If this didn't happen, JS would stop at the execution of fetch if the promise returned was pending or if something went wrong. 

However, my function says, "Hey JS! So go ahead and fetch the communities from the API, while you do that I'll keep executing code. IF you get something from the fetch, put it in this resp variable."

And that is invaluable because now you can make multiple things happen so that they all happen at once or exactly when you need them!

```
...
const data = await resp.json();
...
```

This await is a little more direct. The previous fetch returns a Response (which is stored in the 'resp' variable) and now I want the function to take that response and returns a new Promise with the result parsed with json. 
More technically:
> It returns a promise that resolves with the result of parsing the body text as JSON. 
> -from MDN

This makes taking the now 'data' variable with json reponse easier to use JS to manipulate the DOM. 

and that's IT! 

Async makes JS asynchronous, meaning that JS can execute other commands without breaking. Await makes that possible by waiting for the response of the particular command! 

THIS IS CHALLENGING TO COMPLETELY UNDERSTAND. 
Trust me, I know.
Need a little more help, try this: [JavaScript Visualized: Promises & Async/Await](https://dev.to/lydiahallie/javascript-visualized-promises-async-await-5gke)

## What about events? 

Events is a concept that is incredibly complex and would take multiple blog posts to explain completely. However, at it's core, it's important that understand that Events is what make the world go round! (I mean Javascript)

Understand that an event is anything like: mouse clicks, mouse overs, submitting a form, or even loading the DOM. 

Ok, easy... but why do we care? 

Because of moments like this:

```
function makeCommunity() {
  event.preventDefault();
  let input = event.target.parentElement.parentElement.querySelector("#communityName")
    .value;
  fetch(`${BASE_URL}communities`, {
	...
```

![](https://media.giphy.com/media/wligu2WFLjV9S/giphy.gif)

I KNOW, right? This is a single example of event manipulation and why understand events can be extremely useful.

What's happening here is that once the submit button is clicked from the creating a community form this function is being called. Technically, you can be very explicit and say `function makeCommunity (event) {` or `function makeCommunity(e){` 

`event.preventDefault()` is important because a submit event would refresh the page; this prevents that. 
Then is the fun part.

Event has a target (meaning the submit button) and that target has a parent (in my case, the div where the button lives and that div has a parent (which is the form div). This means you can go up and then queryselect the id of the input that you need the value for! 

Events are also important for understanding WHEN and WHY you want something to happen.

```
document.addEventListener("DOMContentLoaded", () => {
...
```

This is a great example: I need the DOM content to load so that the community list can be displayed and it doubles as a way to automatically display the communities instead of waiting for user input! 

And that makes JS so versitle. Events make it possible to listen for something and make the magic HAPPEN! 
