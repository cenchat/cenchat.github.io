---
layout: post
title: "Setting up on your website"
description: "This quickstart will show you how to set up Cenchat on your website."
cenchat_id: "docs_1"
---

## Getting started

This quickstart will show you how to set up Cenchat on your website.

Before starting, you'll first need to create a [Cenchat Site](https://web.cenchat.com/sites/new), if you haven't done so already. Once you've created one, you'll have to take note of your Site ID (marked in red in the image below).

![Site UI Example]({{ "/assets/images/docs/setting-up-on-your-website/0.png" }})

## Create your start chat button

Cenchat requires you to have your own *start chat* button. This is what your audience will click whenever they want to open Cenchat for that particular web page. You have full control on how you want it to look and where you want it to be placed.

In your *start chat* button, you  **must** add the attributes `id`, `data-site-id`, and `data-page-id`. It should look something like this:

```html
<button id="cenchat-widget-button" data-site-id="zaZMwSXvob53aqvjek4h" data-page-id="1">Start Chat</button>
```

- **Site ID:** This is the ID of your Cenchat Site you've created earlier.
- **Page ID:** This is the ID of your web page which you'll have to provide manually. **Make sure that it's unique across your entire website**.

## Include the widget on your webpage

To include the widget on your webpage, add the code below at the end of your HTML file's `<body>` tag:

```html
<script src="https://widget.cenchat.com/embeds/1.0.0/universal.js"></script>
```

This script will be responsible for actually hooking up Cenchat to your *start chat* button.

## WordPress plugin

To make it easier to integrate Cenchat into WordPress sites, we're currently developing a plugin for it.

This page will be updated once it's available.
