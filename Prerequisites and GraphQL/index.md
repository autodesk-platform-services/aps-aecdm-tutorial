---
layout: page
title: Requisites and GraphQL
nav_order: 2
has_children: true
permalink: /prerequisites/home/
---

# Prerequisites

Before jumping to action, there are a few (but really important) prerequisites you need to fulfill in order to be able to follow along with this tutorial. **The steps need to be fulfilled in sequence for you to be able to follow this tutorial (only move to the next step after completing the previous one)**:

1. First of all, you'll need an **AMER region** ACC account. The AEC Data Model API works on top of Revit 2024 designs hosted on **AMER region** ACC hubs, so it is **required**. Even though the scope of this tutorial is just reading data, we recommend that you create a separate project for testing the AEC Data Model API. If you don't have access to an **AMER ACC hub**, you can find the options to obtain one account for testing at [this blog](https://fieldofviewblog.wordpress.com/2017/08/31/bim-360-acc-account-for-development/). **Please ensure that you now have access to an ACC hub in AMER region and only after that proceed to the next prerequisite.**

2. Once you get access to your hub, you'll need to enable it to generate AEC Designs from uploaded Revit 2024 files. To make this possible, **the account owner** of the ACC hub you're interested in using needs to [join the AEC Data Model API beta](https://feedback.autodesk.com/key/AECDataModelPublicBeta). **We recommend doing this as soon as possible as this process might take up to three business days and after that, there are still prerequisites to be fulfilled before the bootcamp. So, please reach out to your ACC hub owner and ask him/her to [join the AEC Data Model API beta](https://feedback.autodesk.com/key/AECDataModelPublicBeta) as soon as possible!** When your ACC account is enabled, every time you upload a new Revit 2024 file to your hub, it will generate one equivalent AEC Design. This only works against files uploaded **after your hub is allow listed**. Please, reach out to us in case it takes longer than expected. The process works just like in the diagram below:
   ![translation diagram](../../assets/images/translationdiagram.png)
   ![translation diagram](../assets/images/translationdiagram.png)

3. With the first two steps covered, it's time to make our design data ready to use with the AEC Data Model API. For our tutorial, we prepared a subset of files that you can download [here](). You just need to upload those files to your account **at least one week prior to the tutorial date**. We are asking this because the translation process can take some time and if everybody submits all their files without enough time, some designs may not get translated in time. If your account is allow listed, please go ahead, **[download the files](), and upload them to a known project in your ACC AMER account** as soon as possible. After that, you can move to our last prerequisite (we're almost done ;)).

4. You'll also need to [provision access in your ACC hub](https://tutorials.autodesk.io/?check_logged_in=1#provision-access-in-other-products) to the client id `HKVjhUXySDGLGJimolxAgDdpoCuZLlql`. This is the client id of the APS app used by the [explorer](https://aecdatamodel-explorer.autodesk.io/) that we'll be using in this tutorial.

Now let's cover a quick introduction on GraphQL.

# GraphQL

The way the AEC Data Model is structured makes it a perfect match for GraphQL.

For those of you who are not familiar, it is a query language for APIs.
It gives you the power to ask for exactly what you need and nothing more.

With one single endpoint, it makes fetching data easier as you don't need to make requests to multiple endpoints to obtain a set of data.

The request specifies precisely the data that’s going to be returned

Let's compare one use case where we need to check the length of one specific element from a specific version of a specific design.

With the AEC Data Model GraphQL endpoint, the query looks like this:

```js
query {
  aecDesignByVersionNumber(designId: "YWVjZH5...EpfSHZ3", versionNumber:1) {
    elements(filter:{query:"'property.name.External ID'==41434aa5-...-0018527b"}){
      results{
        properties(filter:{names:["Length"]}){
          results{
            name
            value
            propertyDefinition{
              units
            }
          }
        }
      }
    }
  }
}
```

And the response for this query will look like this

```js
{
  "data": {
    "aecDesignByVersionNumber": {
      "elements": {
        "results": [
          {
            "properties": {
              "results": [
                {
                  "name": "Length",
                  "value": 11.000000000000002,
                  "propertyDefinition": {
                    "units": "Meters"
                  }
                }
              ]
            }
          }
        ]
      }
    }
  }
}
```

Note that in this case we basically specified that we wanted to obtain the AEC Design with id `YWVjZH5...EpfSHZ3` from version `1`.
From this design, we specified the element with `External Id` equal to `41434aa5-...-0018527b`, and from this element, we retrieved the property with a name equal to `length` including its **value**, **name**, and **unit**.

With REST API we would need additional requests, and wouldn't be possible to specify with this precision the data in the response.

To summarize, with GraphQL we have the benefits below:

- Single REST API endpoint – You only need to send your query to this endpoint when coding it in your applications
- No fixed Structure for the exchange of data – as compared to the Model Derivative REST API, where you will get a large JSON dataset, that you need to understand and be able to find the data you are looking for
- No over-fetching – as compared to the Model Derivative REST API, where you may need to call various APIs several times to get the data you are looking for.
- Efficiently using resources – Because the GraphQL implementation is on the Autodesk server side, it handles the requests to get the data you are asking for. This minimizes the traffic and allows us to optimize without disruption to the GraphQL aspects.

Before moving to the next step, let's run our very first query.

For that, you just need to go to the explorer app at `https://aecdatamodel-explorer.autodesk.io/`, log in, and run the query from the very first panel (GetHubs), just like in the image below:

![First Query](../../assets/images/firstquery.gif)
![First Query](../assets/images/firstquery.gif)

If you don't see your ACC hub listed, please double check step 3.
Once you see your hub, you can move on to the next step.

[Next Step - Explorer and First Queries]({{ site.baseurl }}/explorer/home/){: .btn}
