---
title: GitHub Pages
---

* toc
{:toc}

## TL;DR

- We can learn about something new (GitHub Pages) and bring together other skills we've learned at the same time 

## Motivating use case: a simple website with GitHub Pages


GitHub Pages is a service provided by GitHub that will turn a GitHub repo into a static website. It's used a lot for personal websites, project documentation, and other websites that don't need heavy server-side software. We can use the stuff we've learned so far to very quickly and easily start working on a little website.

## Skills we'll exercise

- Basic git workflow: `git {clone, status, add, commit, push, pull, checkout, log}`
- Next steps with git: `branch` and `merge` and the `.gitignore` file
- Basic shell navigation
- Develop on a dev branch and merge to master when we're ready
- Let's assume we already have our [ssh keys](keys.md) set up

**Out of scope**: Docker, APIs, unit testing, logging

## GitHub Pages

- [GitHub Pages](https://guides.github.com/features/pages/) is a web hosting service built into GitHub.
- You can use it in three ways:
    - Generate the site from the `master` branch. The entire repo is used to generate the website.
    - Generate the site from the `/docs` folder of the `master` branch. Do this when you have project code with docs in the same repository.
    - Generate the site from the `gh-pages` branch. If you do this, you have a whole separate branch for generating the site.
- We'll just do the first here, so the entire repo is just used to create the site.

