Outline the `why40million` workflow

> What will we do about it?
>
> Web site. Daily content updates. Automatically search for “40 million” and compute a narrative that ties together 2-4 search results we haven’t talked about in the past 7 days.

# Why

You can do it for the practice, or you can do it for the fun backstory. Or both.

For more information about the why40million project and why we're doing it, checkout [this project's homepage](https://solvaholic.github.io/why40million/).

# What

In general, what steps will `why40million` follow?

## StartJob

Something has to start the daily process. Go!

## GetPages

Go find pages and posts that mention "40 million". Ingest them for sorting and selection.

## SelectPages

Sort/filter/select 2-4 pages to use for this run. Select resources we haven't used in the past 7 days.

## GenerateStory

Read the selected pages and compute a narrative that ties them together.

## PublishStory

Publish the story. Um .. put it where visitors can browse to it. Maybe also announce it somewhere.

## FinishJob

Probably we're just done. But if there's something to clean up or put away then do that here.

# How

[The above section](#what) outlines the general steps. How will `why40million` perform each of those?

We'll hash that out in [notes.md](notes.md) and bring implemented solution details here.

## GenerateStory

Read the selected pages and compute a narrative that ties them together.

TODO: Fill in that "dot dot dot".

* Given some configuration and a document URL:

  ```
  CUSER=<UserName>
  CPASS=<Password>
  NLU_URL="https://gateway.watsonplatform.net/natural-language-understanding/api"

  cat > /tmp/payload.json <<EOM
  {
    "url": "https://www.snopes.com/russians-drill-nuclear-strike/",
    "features": {
      "concepts": {
        "limit": 20
      },
      "entities": {
        "limit": 20
      }
    },
    "clean": true,
    "return_analyzed_text": true
  }
  EOM
  ```

* Distill the essence of the sample documents:

  ```
  curl -X POST --user "$CUSER":"$CPASS" \
  --header 'Content-Type: application/json' \
  --header 'Accept: application/json' \
  -d @/tmp/payload.json \
  "$NLU_URL/v1/analyze?version=2017-02-27"
  ```

* Dot dot dot.
* Profit.
