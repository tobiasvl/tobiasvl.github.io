---
title: Obsidian
excerpt_separator: <!--more-->
header:
  teaser: /assets/images/
  og_image: /assets/images/
categories:
  - Blog
tags:
  - blog/draft
tags:
  - PKM
published: false
synced: true
---
I recently started using [Obsidian](https://obsidian.md) as a XXXX.<!-- more --> A lot can be said about how to use it as a Personal Knowledge Manager (PKM), but I'm still finding my way there. However, I've already covnerted several of my other workflows into Obsidian.
## Blogging

The blog you're currently reading is automatically generated with Jekyll by GitHub Pages. I write the blog post in Markdown, which is great, but I generally just use Vim and Git in a terminal. It works fine, don't get me wrong, but I wanted to get as much as my writing as possible into Obsidian.

Since my blog is Markdown, and Obsidian uses Markdown, I just plopped all my blog posts into Obsidian. I planned to simply write the posts in Obsidian, but continue to manually commit and push them with Git from the command line.

I quickly realized that the YAML frontmatter of Jekyll and Obsidian are subtly incompatible. For example, Jekyll supports `tags` with spaces in them (such as my tag [Year in Review](/tags/#year-in-review)), which is made into a tag without spaces (`#year-in-review`) behind the scenes. Obsidian doesn't support tags with spaces. So when I imported my blog posts, I got one tag called `#Year`, one `#in` (that one's useful) and one `#Review`.

The easiest solution would be to change the tag to `#year-in-review`, which would still map to the same slug on the website and not break existing links, but it would look less human readable.

Luckily, I found a great plugin called [GitHub Publisher](https://github.com/ObsidianPublisher/obsidian-github-publisher#tags), which can fix the tag issue _and_ automate pushing to GitHub for me.

## Tasks

## Journaling

I keep a daily journal

## Miscellaneous notes

Today I keep my notes several places:
- Post-its on my desk
- Google Keep
- Google Drive