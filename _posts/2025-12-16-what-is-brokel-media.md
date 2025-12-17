---
title: What is Brokel Media? (Technical Deep Dive)
date: 2025-12-16 12:00:00 +0100
categories: [Tech, "About brokel-media"]
tags: [jekyll, github-pages, architecture, documentation]
---

This article serves as the definitive guide to what **Brokel Media** is, how it is built, and how it operates. It is intended to be a reference for future development, design decisions, and deployment strategies.

## The Concept

**Brokel Media** is a "friends-only" blog and digital garden. It is a collective space for a group of friends to share thoughts, experiences, projects, and "random ass stuff" without the pressure of algorithms, ads, or the broader internet attention economy.

It prioritizes personal connection and freedom of expression over engagement metrics.

## Technical Architecture

The site is a static site hosted on **GitHub Pages**, built using **Jekyll**.

### Core Stack

*   **Static Site Generator:** [Jekyll](https://jekyllrb.com/) (Ruby-based)
*   **Theme:** [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) (A minimal, responsive, and feature-rich theme)
*   **Hosting:** [GitHub Pages](https://pages.github.com/)
*   **Repository:** `brokel-media/brokel-media.github.io`
*   **Branching Strategy:** The `main` branch contains the source code. GitHub Actions (or the built-in Jekyll build process) deploys the site.

### Directory Structure

*   `_config.yml`: The main configuration file for Jekyll and the Chirpy theme. Contains site metadata, SEO settings, and plugin configurations.
*   `_posts/`: Contains the blog posts in Markdown format. Naming convention: `YYYY-MM-DD-title.md`.
*   `_tabs/`: Contains static pages like About, Archives, Categories, etc.
*   `assets/`: Stores static assets like CSS, JavaScript, and images.
*   `tools/`: Contains shell scripts for local development and testing.
*   `contribute/`: A custom web-based editor for non-technical contributors.
*   `_plugins/`: Custom Ruby plugins (e.g., `posts-lastmod-hook.rb` for updating modification dates based on Git history).

## The "Contributor" System (The Editor)

One of the unique technical features of Brokel Media is the custom web-based editor located in the `contribute/` directory. This allows friends who are not comfortable with Git or Markdown to write and publish posts directly from the browser.

### How it Works

1.  **Client-Side Auth:** The system uses a "password" to decrypt a stored **GitHub Personal Access Token (PAT)** directly in the user's browser.
    *   The configuration is generated using `contribute/setup.html`.
    *   The encrypted token is stored in `contribute/config.js` (or similar).
    *   **Security Note:** The PAT is never sent to a backend server (since there isn't one). It is decrypted locally using the password provided by the user.
2.  **GitHub API:** Once decrypted, the PAT is used to authenticate requests to the GitHub API.
3.  **Content Management:**
    *   The editor fetches existing posts from the `_posts/` directory via the GitHub API.
    *   It uses **SimpleMDE** for a user-friendly Markdown editing experience.
    *   When a user saves a post, the editor commits the changes directly to the repository via the GitHub API.

## Development & Deployment

### Local Development

To run the site locally, we use the `tools/run.sh` script.

```bash
./tools/run.sh
```

This command:
1.  Executes `bundle exec jekyll s -l`.
2.  Binds to `127.0.0.1`.
3.  Supports a `-p` flag to run in production mode (`JEKYLL_ENV=production`).

### Testing

To build and test the site, we use `tools/test.sh`.

```bash
./tools/test.sh
```

This script builds the site into the `_site` directory and typically runs `html-proofer` to check for broken links and valid HTML.

### Deployment

Deployment is handled automatically by GitHub Pages. When a commit is pushed to the `main` branch (either via Git or the web editor), GitHub triggers a build and deploys the static files to `brokel-media.github.io`.

## Customizations

*   **Last Modified Date:** A custom hook (`_plugins/posts-lastmod-hook.rb`) updates the `last_modified_at` field in the post front matter based on the git commit history.
*   **Aesthetic:** Various CSS and layout tweaks have been made on top of the Chirpy theme to fit the specific aesthetic of the group.

## Future Considerations

*   It is not good that the repo is public and the posts are public.
*   The comments hosted on Giscus work well, but I do not like that they are in a public discussion.
*   In general with all the features we need it is interesting if it would be better to switch from a static website to a single page application or a dynamic website
*   Regarding hosting I am thinking of hosting it on a Pi in the future and for now using Azure Student for hosting.
*   I am not really interested in the front end, so I would like to keep it as is. But building a backend would be interesting. What languages would be options?
*   Also, I would like to restict access to the website.