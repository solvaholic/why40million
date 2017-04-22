---
layout: default
title: Outline the `why40million` workflow
date: 2017-04-22 09:00:00
tags: why40million
---

# {{ page.title }}

* StartJob
  * Scheduled job calls initial API
* GetPages
  * Get list of URLs
  * Hash URLs for database keys
  * Push URL-and-hash table into MQ?
    Call SelectPages with URL-and-hash table?
* SelectPages
  * Filter out pages we've used in the past 7 days
  * Select a suitable pair of pages
    ```
    pair-found = False
    While not pair-found
      If first-two-pages in used-pairs then pop second-page
      Else pair-found = True
    ```
  * Update first-page and second-page in used pages, with date
  * Add pair to used-pairs, with date
* GenerateStory
  * Convert URLs to input text?
  * Call language generator to make title?
  * Call language generator to make body
  * Convert result to Markdown
  * Add appropriate front matter, for example:
    ```
    ---
    layout: default
    title: Catchy or distracting title
    date: 2017-02-06 21:13:41
    tags: why40million 5-topics-from-the-docs
    ---
    ```
* PublishStory
  * Push the .md file as
    `solvaholic/why40million/_posts/YYYY-MM-DD-Title.md`
* FinishJob
  * Close connections, sweep up crumbs, etc?
