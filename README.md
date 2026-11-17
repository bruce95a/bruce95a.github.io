# BSD Journey

## Introduction

A site about using BSDs. Using hugo.

## Quick start

* init with hugo
```sh
hugo new project blog
```

* or clone from a git repo
```sh
git clone gitrepo
```

* add theme
```sh
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

* add a post
```sh
hugo new content content/posts/my-first-post.md
```

* start server
```sh
hugo server --buildDrafts
```

* build
```sh
hugo build --cleanDestinationDir --gc --minify
```

* publish
```sh
scp -r public/* server:/path/to/webroot
```
