---
layout: doc
title: Setting up on your blog
description: This quickstart will show you how to easily set up Cenchat on your blog.
cenchat:
  id: docs_1
---

This quickstart will show you how to easily set up Cenchat on your blog.

Before anything else, you will need to register and create an account in [our app](https://web.cenchat.com).

## Create a Cenchat Site

Per every blog that you have, you will need to create a corresponding Cenchat Site for it. It's there where you can configure how it will look like so that it would blend well with your design.

To create a Cenchat Site, go [here](https://web.cenchat.com/sites/new).

![Site UI Example]({{ "/assets/images/docs/setting-up-on-your-website/0.png" }})

## Integrate on WordPress Blogs

To make it easier to integrate Cenchat into WordPress blogs, we have an official plugin on [wordpress.org](https://wordpress.org/plugins/cenchat-comments/).

## Custom Integration

Cenchat isn't just limited to WordPress blogs as it can work on any type of website. You'll however need to have basic knowledge with web technologies so it might be a good idea to reach out to your developer if possible.

To set it up manually, follow the instructions below:

### 1. Create a 'Start Chat' Button

Cenchat requires you to have your own *start chat* button. This is what your audience will click whenever they want to open Cenchat for that particular web page. You have full control on how you want it to look and where you want it to be placed.

In your *start chat* button, you  **must** add the attributes `id`, `data-site-id`, and `data-page-id`. It should look something like this:

```html
<button id="cenchat-widget-button" data-site-id="zaZMwSXvob53aqvjek4h" data-page-id="1">Start Chat</button>
```

- **Site ID:** This is the ID of your Cenchat Site you've created earlier.
- **Page ID:** This is the ID of your web page which you will have to provide manually. **Make sure that it's unique across your entire blog**.

### 2. Include the Widget on Your Webpage

To include the widget on your webpage, add the code below at the end of your HTML file's `<body>` tag:

```html
<script src="https://widget.cenchat.com/embeds/1.0.0/universal.js"></script>
```

This script will be responsible for actually hooking up Cenchat to your *start chat* button.
