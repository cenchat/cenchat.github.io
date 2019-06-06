---
layout: post
title: How to setup Ember FastBoot in Cloud Functions for Firebase
description: Ember FastBoot allows you to have server-side rendering for your Ember app. But rather than setting up your own server, you can actually deploy it to functions as a service (FaaS) like Google Cloud Functions.
author: Mikko Paderes
tags: engineering ember
cenchat:
  id: posts_2
---

Using Cloud Function for your server-side app can definitely be cheaper that setting up your own server. This is because it only gets executed when needed. Otherwise, it just sleeps which practically costs you nothing. Not every app is the same though so your mileage may very.

Before we proceed further, be sure you're familiar with [Ember FastBoot](https://ember-fastboot.com) and [Cloud Functions for Firebase](https://firebase.google.com/products/functions/) first.

## Getting started

Obviously, there's 2 things you need to set things up.

1. A FastBoot enabled Ember app.
2. A Node.js server powered by Cloud Functions that would execute your Ember app for rendering the initial HTML and serve it to the user.

For the server, there's 3 ways that you can implement it.

1. [Programmatic Rendering](https://github.com/ember-fastboot/fastboot).
2. [Express Middleware](https://github.com/ember-fastboot/fastboot-express-middleware).
3. [Application Server](https://github.com/ember-fastboot/fastboot-app-server) which is a full blown [Express](https://expressjs.com) server that has most of the hard parts already done for you.

We can't use the Application Server as we already have an existing Express stack by default from Cloud Functions. Instead, we'll use the Express Middleware.

*If you want more flexibility on how FastBoot renders, you can use the programmatic rendering.*

## Setup Cloud Functions

Assuming that your Cloud Firestore directory structure looks like this:

```
+-- functions
|   +-- node_modules
|   +-- index.js
|   +-- package-lock.json
|   +-- package.json
+-- .firebaserc
+-- firebase.json
```

Inside the functions folder, install **express** and **fastboot-express-middleware** package.

```bash
npm install express fastboot-express-middleware
```

Next, create an **app** folder under functions then copy and paste your Ember app build there.

In your **functions/index.js** files, make it look something like this.

```javascript
const express = require('express');

const fastbootMiddleware = require('fastboot-express-middleware');
const functions = require('firebase-functions');

const app = express();

// This points to the location of your app which is in functions/app
const distPath = 'app';

// This makes all your routes to be served in FastBoot
app.get('/*', fastbootMiddleware(distPath));

// This is necessary so you could serve your apps assets
app.use(express.static(distPath));

exports.app = functions.https.onRequest(app);
```

By this time, FastBoot should *somehow* be working in your local Cloud Functions. Try it out, run `firebase serve`.

What you'll notice is that FastBoot works but the assets that's responsible from taking over your app in the client side aren't downloaded. This is because it's looking for those assets under the root directory (e.g. `http://localhost:5000/assets/vendor.js`). To fix that, we'll use [Firebase Hosting](https://firebase.google.com/docs/hosting/) to rewrite our rules and direct our requests to a specific function.

In your **firebase.json** file, set your `hosting` section like this.

```json
{
  "hosting": {
    "rewrites": [{ "source": "**", "function": "app" }]
  }
}
```

This rewrites all our requests and direct it to the app function.

Let's try it out again, run `firebase serve`.

You'll notice this time that the app is being served at `http://localhost:5000` which is actually what we want. Everything should be working properly now.

## Deploying

Let's first setup our `public` folder for Firebase Hosting as this is required when deploying your project.

In your **firebase.json** file, simply add `"public": "public"` in the hosting section.

```json
{
  "hosting": {
    "public": "public",
    "rewrites": [{ "source": "**", "function": "app" }]
  }
}
```

Next, we need to create that **public** folder. Our folder structure should now look something like this.

```
+-- functions
|   +-- app
|       +-- assets
|           +-- vendor.js
|           +-- vendor.css
|           +-- ...
|       +-- index.html
|       +-- package.json
|       +-- robots.txt
|   +-- node_modules
|   +-- index.js
|   +-- package-lock.json
|   +-- package.json
+-- public
+-- .firebaserc
+-- firebase.json
```

In essence, the **public** folder is just a placeholder to satisfy the requirements for deploying to Firebase Hosting.

By now you're probably wondering, can't we just do `"public": "functions/app"`? No you can't because Firebase will consider this as static contents. Thus, it would just return it as-is without executing Cloud Function to do the server-side rendering.

Finally, with all those done, run `firebase deploy` and that should be it.

## Wrapping up

As I mentioned above, not every app is the same. It might make sense to use FaaS on some apps and it might not for others.

One additional thing to take into consideration for this is the *cold start* issue with serverless functions. This is a problem across similar services like AWS Lambda and Azure Functions. What happens is that when functions run for the first time, it's being initialized from scratch. This can take some time to complete hence the *cold start*. If the function wasn't triggered for some time, it goes to sleep and repeats the *cold start* process again once triggered.

Now you might be thinking why would we want to use Cloud Functions for FastBoot, wouldn't that defeat the purpose of *booting fast*? Yes, but only the first unfortunate user would get that effect. If your functions are continuously getting triggered then everything would be smooth for the rest of your users. So as you can see, the higher the traffic of your site, the less chance of a *cold start*.

Ideally, your app would be a PWA. Once they've been stored offline, they wouldn't be hitting your Cloud Functions ever again which makes you avoid that *cold start* issue.
