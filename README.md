# Blog
Another web repository but for a blog

# Creating new content

```shell
    hugo new content posts/{nameOfPost}.md
```

# Local Development

This command will build all the blog contents include drafts

```shell
    hugo server --buildDrafts
```

# Building and Deploying

```shell
    hugo
```

Merging and commiting into the `main` branch will trigger Github Actions to build and deploy the website

# Adding math notation for blog posts

To enable math notation for particular posts, add the following to the frontmatter.
```
math = 'katex'
```

For example:
```markdown
---
title = 'Writing math with Hugo'
tags = ['math']
math = 'katex'
```

Inline math notation are enclosed between pairs of `$`. For example $n$ will be displayed as italicised `n`.

To display math in it's own line, enclose it between pairs of `$$`. We can use `KateX` an `LaTeX` notation to display
math.
