---
title: github.io blog setup using jekyll & chirpy theme
date: 2024-01-09 00:33:00 +0900
categories: [TOOLS, BLOG]
tags: [github-io, jekyll, chirpy, blog, ruby, troubleshooting]
---

I always wanted my own `github.io` tech blog. Finally, enough procrastination, and here I am.

As I searched for different ways to set everything up, I thought it would be a good idea to start my first post with the information I gathered along my way.

Wasn't too difficult, but also not so straight-forward compared to other commercial solutions.

Enjoy! 😄

## Environment

---
+ Mac OS Monterey 12.6.3
+ zshell
+ This tutorial is effective as of January 2024

## Prerequisites

---
> This tutorial assumes the audience to be familiar with the following concepts/tools.
{: .prompt-info }
+ Basic Git Commands (git add, commit, push, pull)
+ Owns a GitHub account

## Pick the theme

---
The greatest advantage of `github.io` is that you get to cherry-pick the customizable capability of hosting your own web service, and at the same time piggyback on top of well-designed formats.

Before we start, we must pick a theme to piggyback on. Here are some links that have a popular list.
- [https://github.com/topics/jekyll-theme](https://github.com/topics/jekyll-theme)
- [https://jekyll-themes.com/](https://jekyll-themes.com/)
- [https://jekyllthemes.io/free](https://jekyllthemes.io/free)

There are many candidates, so it is best if you have a clear standard for your choice. For me, it was:
- Simple (not so fancy) layout
- Category field in LNB (Left Navigation Bar)
- Clean basic font, code font
- Clean Korean font (as I write Korean posts too)
- Has an "About" page 

My final choice was [chirpy](https://chirpy.cotes.page/)!

It requires you to have ruby, jekyll, and git.

## Install ruby, jekyll

---
As it turns out, the free `github.io` world is mostly built with something called jekyll, a package in ruby.

> I have ZERO experience with ruby whatsoever, so don't be freaked out here. 
{: .prompt-warning}

According to the [official ruby docs](https://www.ruby-lang.org/en/downloads/), one of the recommended ways to install ruby is [rbenv](https://github.com/rbenv/rbenv), so I followed their advice.

> TIL: macOS systems have ruby installed by default, but ruby people strongly discourage using it. They even have a [website](https://dontusesystemruby.com/#/) for it! 😅
{: .prompt-info}

I suppose rbenv is ruby's equivalent to pyenv, to manage multiple installed ruby versions.

```bash
brew install rbenv ruby-build
```

You can check a stable version with the below command, and install one. I chose `3.1.4`.

```bash
# list latest stable versions:
rbenv install -l
```
```bash
# install a Ruby version:
rbenv install 3.1.4
```

Set the version you have installed to the global version, and check whether your ruby is looking at the one we just installed, instead of the system ruby.

```bash
rbenv global 3.1.4
```
```bash
# should NOT be /usr/bin/ruby, but something like /Users/username/.rbenv/shims/ruby
which ruby
```

As I use zsh, I added the `rbenv init` command to `.zshrc`.
```bash
echo 'eval "$(/opt/homebrew/bin/rbenv init - zsh)"' >> ~/.zshrc
```

Check everything is in place. Gem is ruby's package manager which should already have been installed together with your ruby installation.
```bash
ruby -v
gem -v
```
Time to install jekyll.
```bash
gem install jekyll
```

## Fork theme repo

---
From here follow [chirpy doc's getting started guide](https://chirpy.cotes.page/posts/getting-started/). Very instructive and well-illustrated.

Out of the two choices given by chirpy (chirpy starter, GitHub fork), I have chose to GitHub fork for customization. Clone your forked repository to local and work from there.

> Note that the repository name after forking must be renamed to `[GitHub username].github.io`
{: .prompt-info}

> If your branch and `origin/master` has diverged and conflicting after the `bash tools/init` command, accept YOURS(local `master`) to resolve conflicts as it has removed unnecessary files.
{: .prompt-warning}

## Local testing & tweaking

---
Local changes to view your changes live can be done with the following command.
```bash
bundle exec jekyll s
```

## Deployment

---
Deployment is smoothly available with triggering GitHub Actions on the top of your repository page. Usually any pushes to the `master` branch will trigger a new GitHub CICD action, but you can also trigger manually.

## Optional: Customize LNB

---
Although everything is simple and smooth now, I wanted to tweak the left navigation bar so that:
+ It has a background image
+ Text colors are lighter to match the background

I am no css expert, so I had to tweak `_sass/addon/commons.scss` line by line, and deploy locally to see each selector's effect.

Specific changes can be seen [here in my commit](https://github.com/notiona/notiona.github.io/commit/6ea64ed8c7bcc6714756eb6c1d8c611ef07d1e2a).

## Write awesome posts

---
Please refer to [chirpy's writing guide](https://chirpy.cotes.page/posts/write-a-new-post/) for chirpy-specific and jekyll-specific syntax for writing posts, but generally posts are Markdown files and thus generally follow Markdown syntax.

## Korean Equivalent Post

---
[Jekyll과 chirpy 테마 활용해 github.io 블로그 셋업하기](https://notiona.github.io/posts/github-io-jekyll-ruby-kor/)

## References

---
+ [https://github.com/topics/jekyll-theme](https://github.com/topics/jekyll-theme)
+ [https://www.ruby-lang.org/en/downloads/](https://www.ruby-lang.org/en/downloads/)
+ [https://chirpy.cotes.page/posts/getting-started/](https://chirpy.cotes.page/posts/getting-started/)
+ [https://chirpy.cotes.page/posts/write-a-new-post/](https://chirpy.cotes.page/posts/write-a-new-post/)

---

> Please feel free to point out any inaccurate or insufficient information. Also, please feel free to leave any questions or suggestions in the comments. 🙇‍♂️
{: .prompt-info}
