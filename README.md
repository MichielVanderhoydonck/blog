# Blog

My personal blog built with [Zola](https://www.getzola.org/) and the [Kita](https://github.com/st1020/kita) theme.

## Getting Started

### Prerequisites

You need [Zola](https://www.getzola.org/documentation/getting-started/installation/) installed. On macOS, you can install it via Homebrew:

```bash
brew install zola
```

### Run the Development Server

To start the Zola development server with live reload:

```bash
zola serve --interface 0.0.0.0 --port 2000 -u localhost
```

> [!NOTE]
> The `-u localhost` flag is required so that the browser loads stylesheets and assets from your local server instead of attempting to fetch them from `0.0.0.0` (which is blocked by modern browsers for security reasons).

You can access the local server at [http://localhost:2000](http://localhost:2000).

---

## Writing Content

All blog posts are located in the [content/](file:///Users/michiel/github/blog/content) directory.

### Adding a New Post

1. Create a new Markdown file in the [content/](file:///Users/michiel/github/blog/content) directory, named using the format `YYYY-MM-DD-title.md` (e.g., `2026-07-07-my-new-post.md`).
2. Add TOML front matter at the top of the file:

```markdown
+++
title = "My New Post"
date = "2026-07-07"
description = "A short summary of what this post is about."
[taxonomies]
tags = ["Tag1", "Tag2"]
+++

Your post content in Markdown starts here...
```

### Static Assets

If your post needs images or other static files, place them in the [static/](file:///Users/michiel/github/blog/static) directory (e.g., in `static/images/my-image.png`). You can reference them in your post using relative URLs or Zola's URL resolution:

```markdown
![Alt text](/images/my-image.png)
```

---

## Build for Production

To build a static version of the site ready for production deployment:

```bash
zola build
```

This compiles the entire site into the `public/` directory.
