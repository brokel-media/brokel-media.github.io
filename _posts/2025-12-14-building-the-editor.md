---
layout: post
title: "Building a Password-Protected Editor for a Static Site"
date: 2025-12-14 12:00 +0100
categories: [Tech, Web Development]
tags: [jekyll, github-pages, cryptography]
---

I recently added a feature to this blog that allows my friends to contribute posts directly, without needing to know how to use Git or Markdown. I wanted something simple: a password-protected page where they could just write and publish.

Since this site is hosted on GitHub Pages, it's a "static" site. This means there's no database and no backend server to handle user logins or passwords. Usually, people solve this by using third-party services like Netlify Identity or setting up complex OAuth flows. I wanted to avoid all that complexity and keep everything self-contained.

## The Solution: Client-Side Encryption

I built a custom editor that runs entirely in the browser. Here is how it works under the hood:

1.  **The "Keys"**: I created a special GitHub Access Token that has permission to create files in this repository.
2.  **The Lock**: I didn't want to put this token directly in the code (that would be insecure!). Instead, I encrypted this token using a strong password.
3.  **The Magic**: The encrypted token is stored in a file on this website. When a user visits the `/admin` page, they are asked for the password.
4.  **Unlocking**: The browser uses the Web Crypto API to try and decrypt the token using the password provided. If the password is correct, the token is revealed *in memory* (it's never saved to disk or sent to a server).
5.  **Publishing**: The editor then uses that token to talk directly to the GitHub API, creating a new markdown file in the `_posts` folder.

This approach is secure because the actual access token is never exposed in plain text. It only exists in its encrypted form until the correct password unlocks it inside your browser.

## How to Contribute

If you are a friend and want to write a post:

1.  Go to the **[Admin Page](/admin)**.
2.  Enter the contributor password (ask me for it!).
3.  Write your post in the editor.
4.  Click **Publish**.

The post will automatically appear on the site after GitHub finishes building it (usually takes about a minute). Happy writing!
