Gather notes here while we discover how to implement `why40million`.

# How?

In general, what steps will `why40million` follow? And how will it perform them?

We can trigger the workflow and select documents manually, so if we need to pick a thing to work on first then let's do them in this order:

1. [GenerateStory](#generatestory) - This is the meat. The rest is presentation.
2. [SelectPages](#selectpages) or [PublishStory](#publishstory)
2. [PublishStory](#publishstory) or [SelectPages](#selectpages)
4. [GetPages](#getpages)
5. [StartJob](#startjob)
6. [FinishJob](#finishjob)

## StartJob

Something has to start the daily process. Go!

* StartJob
  * Scheduled job calls initial API

## GetPages

Go find pages and posts that mention "40 million". Ingest them for sorting and selection.

> Note: Usedtowas the Alchemy API looked like a good resource for this. 'Tis no more. ¯\\\_(ツ)\_/¯
>
> Looks like now we want the [Discovery](https://console.bluemix.net/catalog/services/discovery) API.

* GetPages
  * Get list of URLs
  * Hash URLs for database keys
  * Push URL-and-hash table into MQ?
    Call SelectPages with URL-and-hash table?

## SelectPages

Sort/filter/select 2-4 URLs to use for this run. Select resources we haven't used in the past 7 days.

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

1. Get 2-4 documents for input:

    * Get input URLs

  > https://duckduckgo.com/?q=40+million&t=ffab&ia=web
  >
  > - http://www.cnn.com/2017/09/19/world/global-slavery-estimates-ilo/index.html
  > - https://www.wsj.com/articles/boston-penthouse-to-seek-record-40-million-1503408989
  > - https://www.snopes.com/russians-drill-nuclear-strike/

2. Understand (what?) about those documents:

    * Let's use [Watson's Natural Language Understanding](https://console.bluemix.net/catalog/services/natural-language-understanding) and try to find 1-3 concepts and/or entities the docs have in common and 2-4 that are each common to only 1 doc.

  - Concepts and entities should offer more insight than keywords or sentiments (alone) would. The [API reference](https://www.ibm.com/watson/developercloud/natural-language-understanding/api/v1/) describes the various things we can get via the `v1/analyze` API.
  - [NLU tutorial](https://console.bluemix.net/docs/services/natural-language-understanding/getting-started.html#getting-started-tutorial)

  ```
  CUSER=<UserName>
  CPASS=<Password>
  NLU_URL="https://gateway.watsonplatform.net/natural-language-understanding/api"
  CONTENT="I%20still%20have%20a%20dream%2C%20a%20dream%20deeply%20rooted%20in%20the%20American%20dream%20%E2%80%93%20one%20day%20this%20nation%20will%20rise%20up%20and%20live%20up%20to%20its%20creed%2C%20%22We%20hold%20these%20truths%20to%20be%20self%20evident%3A%20that%20all%20men%20are%20created%20equal."
  ```

  - Return overall sentiment plus a list of keywords:

    ```
    curl --user "$CUSER":"$CPASS" \
    "$NLU_URL/v1/analyze?version=2017-02-27&text=$CONTENT&features=sentiment,keywords"
    ```

  - Return overall sentiment plus a list of keywords and per-keyword sentiments:

    ```
    curl --user "$CUSER":"$CPASS" \
    "$NLU_URL/v1/analyze?version=2017-02-27&text=$CONTENT&features=sentiment,keywords&keywords.sentiment=true"
    ```

  - Same as above but targeting a phrase (which means what?):

    ```
    curl --user "$CUSER":"$CPASS" \
    "$NLU_URL/v1/analyze?version=2017-02-27&text=$CONTENT&features=sentiment,keywords&keywords.sentiment=true&sentiment.targets=the%20American%20dream"
    ```

  - Using one of our URLs as input, via the API explorer:

    ```
    curl -X GET --header 'Accept: application/json' 'https://watson-api-explorer.mybluemix.net/natural-language-understanding/api/v1/analyze?version=2017-02-27&url=http%3A%2F%2Fwww.cnn.com%2F2017%2F09%2F19%2Fworld%2Fglobal-slavery-estimates-ilo%2Findex.html&features=concepts%2Centities&return_analyzed_text=true&clean=true&fallback_to_raw=true&concepts.limit=8&emotion.document=true&entities.limit=50&entities.mentions=false&entities.emotion=false&entities.sentiment=false&keywords.limit=50&keywords.emotion=false&keywords.sentiment=false&relations.model=en-news&semantic_roles.limit=50&semantic_roles.entities=false&semantic_roles.keywords=false&sentiment.document=false'
    ```

  - Demonstrating POST method via the API explorer:

    ```
    curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' -d '{ \
       "url": "https://www.wsj.com/articles/boston-penthouse-to-seek-record-40-million-1503408989", \
       "features": { \
         "concepts": { \
           "limit": 20 \
         }, \
         "entities": { \
           "limit": 20 \
         } \
       }, \
       "clean": true, \
       "return_analyzed_text": true \
     }' 'https://watson-api-explorer.mybluemix.net/natural-language-understanding/api/v1/analyze?version=2017-02-27'
    ```

  - Putting together an example:

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

    curl -X POST --user "$CUSER":"$CPASS" \
    --header 'Content-Type: application/json' \
    --header 'Accept: application/json' \
    -d @/tmp/payload.json \
    "$NLU_URL/v1/analyze?version=2017-02-27"
    ```

3. Make up a new story based on (what?):

  > 20171224 - Natural language generation may not yet be the commodity service I thought it was. #TIL
  >
  > For now let's ingest the source documents and pretend that metadata is the generated story. Once we sort this step we can just put it in place.

    * Call language generator to make title and body

  - We could do something like this with Zapier:
    - Receive data payload via webhook.
    - Wordsmith a story about the data.
    - Send the result to .. somewhere.
  - Except, to do that we'd need access to Wordsmith services.
  - There are some Wordsmith tools and examples [on GitHub](https://github.com/ai-wordsmith).

4. Annotate the new story appropriately:

    * Convert result to Markdown
    * Add appropriate front matter, for example:
      ```
      ---
      layout: default
      title: Catchy or distracting title
      date: 2017-02-06 21:13:41
      tags: why40million
      ---
      ```

## PublishStory

Publish the story. Um .. put it where visitors can browse to it. Maybe also announce it somewhere.

> Assumption: We'll publish this story as a post in this project's Pages site or run it through a static site generator and push it to AWS S3.

* PublishStory
  * Push the .md file as
    `solvaholic/why40million/_posts/YYYY-MM-DD-Title.md`

## FinishJob

Probably we're just done. But if there's something to clean up or put away then do that here.

* FinishJob
  * Close connections, sweep up crumbs, etc?
