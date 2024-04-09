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

