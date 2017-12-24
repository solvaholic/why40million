Gather notes here while we discover how to implement `why40million`.

# What

In general, what steps will `why40million` follow?

## StartJob

Something has to start the daily process. Go!

* StartJob
  * Scheduled job calls initial API

## GetPages

Go find pages and posts that mention "40 million". Ingest them for sorting and selection.

* GetPages
  * Get list of URLs
  * Hash URLs for database keys
  * Push URL-and-hash table into MQ?
    Call SelectPages with URL-and-hash table?

## SelectPages

Sort/filter/select 2-4 pages to use for this run. Select resources we haven't used in the past 7 days.

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

## GenerateStory

Read the selected pages and compute a narrative that ties them together.

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

## PublishStory

Publish the story. Um .. put it where visitors can browse to it. Maybe also announce it somewhere.

* PublishStory
  * Push the .md file as
    `solvaholic/why40million/_posts/YYYY-MM-DD-Title.md`

## FinishJob

Probably we're just done. But if there's something to clean up or put away then do that here.

* FinishJob
  * Close connections, sweep up crumbs, etc?
