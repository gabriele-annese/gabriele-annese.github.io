# Install Hugo
[Install Hugo](https://gohugo.io/installation/) (extended edition, v0.112.0 or later)

# Use hugo
Example of how to craete a site with Hugo

```
hugo new site quickstart
cd quickstart
git init
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
echo "theme = 'ananke'" >> hugo.toml
hugo server
```
## Build
To build a hugo project simply type
```
hugo
```
## Add content
Add a new page to your site.
```
hugo new content content/posts/my-first-post.md
```
Hugo created the file in the content/posts directory. Open the file with your editor.

```
+++
title = 'My First Post'
date = 2024-01-14T07:07:07+01:00
draft = true
+++
```
Notice the **draft** value in the front matter is **true**. By default, Hugo does not publish draft content when you build the site. Learn more about draft, future, and expired content.

Add some Markdown to the body of the post, but do not change the draft value.
```
+++
title = 'My First Post'
date = 2024-01-14T07:07:07+01:00
draft = true
+++
## Introduction

This is **bold** text, and this is *emphasized* text.

Visit the [Hugo](https://gohugo.io) website!
```