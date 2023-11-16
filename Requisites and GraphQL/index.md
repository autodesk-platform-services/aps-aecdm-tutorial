---
layout: page
title: Requisites and GraphQL
nav_order: 2
has_children: true
permalink: /requisites/home/
---

# Requisites

Before jumping to action, there a few (but really important) pre-requisites you need to fulfill in order to be able to follow along with this tutorial:

1. You'll need an ACC account allow-listed to generate AEC Designs from uploaded Revit 2024 files. To make this possible, the account owner of the ACC hub you're interested in using needs to join the AEC Data Model API beta, as described by [this blog post](https://aps.autodesk.com/blog/aec-data-model-apis-are-now-public-beta). **We recommend doing this as soon as possible as this process might take up to three business days and after that there are still pre requisites to be fulfilled prior to the bootcamp. So, please reach your ACC hub owner and ask him/her to [join AEC Data Model API beta](https://feedback.autodesk.com/key/AECDataModelPublicBeta) as soon as possible!**. From the moment your ACC acount is enabled, every time you upload a new Revit 2024 file to your hub, it will generate one equivalent AEC Design. This only works against files uploaded **after your hub is allow-listed**. Because of that, it is extremely important that you **only goes through step 2 if your ACC account has been enabled to generate AEC Designs**. Please, reach out to us in case it takes longer than expected.

2. With first step covered, it's time to make our files available for interacting with AEC Data Model API. For that, you just need to upload Revit 2024 designs in your hub. For our tutorial, we prepared a subset of files that you can download [here](). We just need you to upload those in your account at least one week prior to the tutorial date. If your account is allow-listed, please go ahead and **[download the files]() and upload them in a known project in your account**

3. [Provision access in your ACC hub](https://tutorials.autodesk.io/?check_logged_in=1#provision-access-in-other-products) to the client id `HKVjhUXySDGLGJimolxAgDdpoCuZLlql`. This is the client id of the APS app used by the [explorer](https://aecdatamodel-explorer.autodesk.io/) that we'll using in this tutorial.

Now let's cover a quick introduction on GraphQL.

# GraphQL

The way AEC Data Model is structured makes it a perfect match for GraphqL.

For those of you that are not familiar, it is a query language for APIs.
It gives you the power to ask for exactly what you need and nothing more.

With one single endpoint, it makes fetching data easier as you don't need to make requests to multiple endpoints to obtain a set of data.

The request specifies precisely the data that’s going to be returned

Let's compare one use case where we need to check the length of one specific element from a specific version of a specific design.

With AEC Data Model GraphQL endpoint, the query looks like this:

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

Note that in this case we basically specified that we wanted obtain the AEC Design with id `YWVjZH5...EpfSHZ3` from version `1`.
From this design, we specified the element with `External Id` equals to `41434aa5-...-0018527b` and from this element, we retrieved the property with name equals to `length` including its **value**, **name** and **unit**.

With REST API we would need additional request, and wouldn't be possible to specify with this precision the data in the response.

To summarize, with GraphQL we have the benefits below:

- Single endpoint
- No fixed Structure for exchange of data
- No over-fetching
- Efficient resource-wise

Before moving to the next step, lets run our very first query.

For that, you just need to go to the explorer app at `https://aecdatamodel-explorer.autodesk.io/`, login, and run the query from the very first panel (hubs), just like in the image below:

![First Query](../../assets/images/firstquery.gif)

If you don't see your ACC hub listed, please double check step 3.
Once you see your hub, you can move on to the next step.

[Next Step - Explorer and First Queries]({{ site.baseurl }}/explorer/home/){: .btn}
