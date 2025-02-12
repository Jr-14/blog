# Blog
Another web repository but for a blog

# Getting started
**IMPORTANT** Make sure you clone this repo correctly as it relies on [papertheme](https://themes.gohugo.io/themes/hugo-paper/)
```
git clone --recurse-submodules <REPO_URL>
```

## Dependencies
- [golang](https://go.dev/doc/install) as dependency for hugo.
- [Hugo](https://gohugo.io/) as the static site generator for the blog. I think we use the extended edition? 

# Development

## Starting development server
Hugo contents generally have the `draft` front matter set to `true`. To include `draft` contents run one of:
```
hugo server --buildDrafts
hugo server --D
```

## Adding a content
```
hugo new content posts/<content-name>.md
```

## Adding math support to the content
You can add rendering of math by adding the following to the front matter.

Using yaml:
```
---
...
math: 'katex'
...
---
```

You can also use toml and json. Find the structure here: https://gohugo.io/content-management/front-matter/

# Publishing Your Site
Run
```
hugo
```
