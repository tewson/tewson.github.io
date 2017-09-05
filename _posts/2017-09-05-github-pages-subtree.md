---
layout: post
title:  "Deploying to GitHub Pages with git worktree"
date:   2017-09-05
---

[GitHub Pages](https://pages.github.com/) is a great service for showcasing your GitHub projects. Chances are, you want to keep the actual project source code and the GitHub pages code separated in different branches. You might also prefer the GitHub pages code to not pollute the project source code. The git command [`git worktree`](https://git-scm.com/docs/git-worktree) (from git 2.5) can help you do just that.

Let's see this in action. First, fork my [example repository](https://github.com/tewson/gh-pages-worktree-example). You'll also need a [Node.js](https://nodejs.org) environment to build the GitHub Pages code.

Clone the forked repository.

`git clone https://github.com/your_user_name/gh-pages-worktree-example`

Enter the repository's root directory.

`cd gh-pages-worktree-example`

Install dependencies.

`npm install`

In this example, we're gonna keep the GitHub Pages code in the `dist` directory and publish it to a new branch called `gh-pages`.

`git worktree add -b dist gh-pages`

If you `cd` into `dist` and run `git status`, you can see that we're now on the `gh-pages` branch.

Go back to the root directory and build the GitHub Pages code.

`npm run build`

You can see that there are no changes to commit to `master`. This is because `dist` is ignored in `master`.

However, when you go into `dist`, there are new files waiting to be committed to `gh-pages`. We're gonna add and commit them.

`cd dist`

`git add .`

`git commit`

`git push origin gh-pages`

Go to the settings of the forked repository on GitHub.com and make sure that your GitHub Pages site is set to be built from the `gh-pages` branch.

You should now be able to see the built GitHub Pages on [https://your_user_name.github.io/gh-pages-worktree-example](https://your_user_name.github.io/gh-pages-worktree-example).
