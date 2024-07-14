---
layout: page
title: How the explorer connects with the Viewer and advanced queries
nav_order: 4
has_children: false
permalink: /connection/home/
---

# How the explorer connects with the Viewer and advanced queries

In this section, we'll understand how the explorer connects the elements retrieved from the response with the elements rendered by the Viewer.
After this, we'll explore more complex workflows supported by the AEC Data Model API.
Let's begin with Viewer.

## Connecting with Viewer

Every time you run a query against a specific design and the same design is rendered in the Viewer, the elements from the response are isolated in the Viewer scene.
To understand the process, let's analyze the diagram below:

![Viewer Connection Process](../../assets/images/viewerconnectionprocess.png)

1. It starts when the user push the button to run a query.
2. In case there's a design rendered in viewer, a method gets triggered when the response is received, just like in the snippet below:

```js
return fetch("https://developer.api.autodesk.com/aec/graphql", {
  method: "post",
  headers: {
    "Content-Type": "application/json",
    Authorization: "Bearer " + accessToken,
    ...headers,
  },
  body: JSON.stringify(graphQLParams),
}).then(async (response) => {
  //This gets triggered as soon as the response is received
  const viewerToggle = document.getElementById("toggleviewer");
  //The response is saved in the global variable queryResponse
  queryResponse = await response.json();
  //Here we check if Viewer is active
  if (!!globalViewer && viewerToggle.checked) {
    //If so
    globalViewer.getExtension("AECDMFilterExtension").filterModel();
  }
  return queryResponse;
});
```

3. The elements are extracted from the AEC Data Model API response. Apart from the id we can see from the response, each element has a persistent id that remains the same in Revit, Viewer, and AEC Data Model. For those familiar with Revit, this is the [UniqueId](https://www.revitapidocs.com/2024/f9a9cb77-6913-6d41-ecf5-4398a24e8ff8.htm). In the response, this id is available in the **External ID** property. We just need to retrieve every value for this property from the response, and the explorer does that using regular expressions through the snippet below:

```js
async retrieveOccurences(jsonResponse) {
  //Here we use a regex to handle the json as an array of strings
  let stringArrayResponse = JSON.stringify(jsonResponse).replace(/{|}|\[|\]/g, '|').split('|');
  let sourceIdIndex = stringArrayResponse.findIndex(s => s.includes('External ID'));
  let externalIds = [];
  let count = 0;
  while (sourceIdIndex != -1 && count < 999) {
    try {
      externalIds.push(JSON.parse(`{${stringArrayResponse[sourceIdIndex].split(',')[1]}}`).value);
    }
    catch (error) {
      console.log('not able to filter one external id');
      console.log(error);
    }
    count++;
    stringArrayResponse.splice(sourceIdIndex, 1);
    sourceIdIndex = stringArrayResponse.findIndex(s => s.includes('External ID'));
  }
  return externalIds.filter(i => !!i);
}
```

> Regex is a good solution as it can address the variations of the response. Keep in mind that in GraphQL the response structure is flexible. ;)

4. The list of ids is passed to the Viewer. From the Viewer's perspective, these values are available also as the **external id** property from each object. Since the Viewer uses the **dbId** in its methods, we can use the [getExternalIdMapping](https://aps.autodesk.com/en/docs/viewer/v7/reference/Viewing/Model/#getexternalidmapping-onsuccesscallback-onerrorcallback) function to retrieve the correlation between **dbIds** and **external ids**.

> We have [this blog](https://aps.autodesk.com/blog/get-dbid-externalid) on external ids in Viewer. Feel free to check it out ;)

5. With the list of **dbIds**, we can simply use Viewer's [isolate](https://aps.autodesk.com/en/docs/viewer/v7/reference/Viewing/Model/#getexternalidmapping-onsuccesscallback-onerrorcallback) and [fitToView](https://aps.autodesk.com/en/docs/viewer/v7/reference/Viewing/GuiViewer3D/#fittoview-objectids-model-immediate) methods to adjust the scene to focus on our elements.

Steps 4 and 5 are achieved with the snippet below:

```js
async dbidsFromExternalIds(externalIds) {
  this.viewer.model.getExternalIdMapping((externalIdsDictionary) => {
    let dbids = [];
    externalIds.forEach(externalId => {
      let dbid = externalIdsDictionary[externalId];
      if (!!dbid)
        dbids.push(dbid);
    });
    this.viewer.isolate(dbids);
    this.viewer.fitToView();
  }, console.log)
}
```

And now you know what happens behind the scenes to filter elements from the response in Viewer. Now we can focus on additional queries.

## Advanced Queries

The AEC Data Model API has great filtering capabilities, and we'll explore that a lot in the next queries. In this section, we'll focus on advanced filtering capabilities, versioning, cross-design querying, and references between elements.

### Advanced Filtering Capabilities

The filters available in AEC Data Model API enable us to filter our queries based on our design metadata, limiting the results to match precisely what we're looking for.

> For future reference, check our documentation on filtering [here](https://aps.autodesk.com/en/docs/aecdatamodel/v1/developers_guide/filtering/) ;)

Suppose you need to count the total length of `Ducts` needed in your project.
To achieve this, you can retrieve the elements (instances) from the `Ducts` category in the **Snowdon Towers Sample HVAC.rvt** design and limit the response to only list their sizes and lengths.

The achieve this, you can use the query below:

```js
query GetDuctsFromElementGroup($elementGroupId: ID!, $elementsFilter: String!) {
  elementsByElementGroup(
    elementGroupId: $elementGroupId
    filter: {query: $elementsFilter}
    pagination: {limit: 100}
  ) {
    pagination {
      cursor
    }
    results {
      name
      properties(filter: {names: ["Diameter", "Length"]}) {
        results {
          name
          value
          definition {
            units{
              name
            }
          }
        }
      }
    }
  }
}
```

With the Variables below:

```js
{
  "elementGroupId": "YOUR DESIGN ID GOES HERE!",
  "elementsFilter": "property.name.category==Ducts and 'property.name.Element Context'==Instance"
}
```

Your response should begin with a cursor, meaning that you'll need to go through all the available pages to retrieve all the elements.

We can point to the next page by simply adding the cursor in our query, like the snippet below:

```js
query GetDuctsFromElementGroup($elementGroupId: ID!, $elementsFilter: String!) {
  elements(
    elementGroupId: $elementGroupId
    filter: {query: $elementsFilter}
    pagination: {limit: 100, cursor:"YOUR CURSOR GOES HERE!"}
  ) {
    pagination {
      cursor
    }
    results {
      name
      properties(filter: {names: ["Diameter", "Length"]}) {
        results {
          name
          value
          definition {
            units{
              name
            }
          }
        }
      }
    }
  }
}
```

The variables remain the same.

### Versioning

If you need to retrieve the elements from a specific version, that's also possible with the `elementsByElementGroupAtVersion` query.
The query would be just like the snippet below:

```js
query GetDuctsFromElementGroupAtVersion($elementGroupId: ID!, $elementsFilter: String!, $versionNumber: Int!) {
  elementsByElementGroupAtVersion(
    elementGroupId: $elementGroupId
    filter: {query: $elementsFilter}
    versionNumber:$versionNumber
    pagination: {limit: 100}
  ) {
    pagination {
      cursor
    }
    results {
      name
      properties(filter: {names: ["Diameter", "Length"]}) {
        results {
          name
          value
          definition {
            units{
              name
            }
          }
        }
      }
    }
  }
}
```

With the Variables below:

```js
{
  "elementGroupId": "YOUR ELEMENT GROUP ID GOES HERE!",
  "elementsFilter": "property.name.category==Ducts and 'property.name.Element Context'==Instance",
  "versionNumber": <<YOUR VERSION NUMBER GOES HERE>>
}
```

### Cross-Design Querying

So far so good. You nailed the previous queries and now your team can easily retrieve the total length of Ducts required for your project even divided by size, but the solution is still incomplete...

There are ducts also in the plumbing sample (used in the water heaters).

What if now you get asked to consider the ducts in **all disciplines**?
Would you run this query in a loop for each design from the project?
No, there's no need for that!
You can simply query the elements from the project level (even from the hub level ;)).

You can achieve that with the query below:

```js
query GetDuctsFromProject($projectId: ID!, $elementsFilter: String!) {
  elementsByProject(
    projectId: $projectId
    filter: {query: $elementsFilter}
    pagination: {limit: 100}
  ) {
    pagination {
      cursor
    }
    results {
      name
      properties(filter: {names: ["Diameter", "Length"]}) {
        results {
          name
          value
          definition {
            units{
              name
            }
          }
        }
      }
    }
  }
}
```

With the Variables below:

```js
{
  "projectId": "YOUR PROJECT ID GOES HERE!",
  "elementsFilter": "property.name.category==Ducts and 'property.name.Element Context'==Instance"
}
```

To handle the pagination using the cursor obtained, you can use the modified query below:

```js
query GetDuctsFromProject($projectId: ID!, $elementsFilter: String!) {
  elementsByProject(
    projectId: $projectId
    filter: {query: $elementsFilter}
    pagination: {limit: 100, cursor:"YOUR CURSOR GOES HERE!"}
  ) {
    pagination {
      cursor
    }
    results {
      name
      properties(filter: {names: ["Diameter", "Length"]}) {
        results {
          name
          value
          definition {
            units{
              name
            }
          }
        }
      }
    }
  }
}
```

This approach covers all the occurences in the entire project :)

### References

Great! You can now extract quantities filtering by category and properties, consider versioning, and even query from multiple designs in one single request.
What if this time you need to do the same, but for one specific level?

To achieve this, we can use the **references** between the elements from our designs.

Please refer to the query below:

```js
query GetDuctsByLevelName($projectId: ID!, $elementsFilter: String!) {
  elementsByProject(
    projectId: $projectId
    filter: {query: $elementsFilter}
    pagination: {limit: 50}
  ) {
    pagination {
      cursor
    }
    results {
      name
      elementGroup{
        name
      }
      referencedBy(name:"Reference Level", filter:{query:"property.name.category==Ducts"}, pagination:{limit:100}){
        pagination{
          cursor
        }
        totalCount
        results{
          name
          properties(filter:{names:["Diameter", "Length"]}){
            results{
              name
              value
              definition {
                units{
                  name
                }
              }
            }
          }
        }
      }
    }
  }
}
```

Variables:

```js
{
  "projectId": "YOUR PROJECT ID GOES HERE!",
  "elementsFilter": "'property.name.Element Name'==L2 and property.name.category==Levels and 'property.name.Element Context'==Instance"
}
```

With the response from this query, you'll obtain a list of elements **referenced by all the instances with the name L2**. From these elements, we filter **the ones from the Ducts category and retrieve only the Diameter and Length properties**.

Now you've covered many possible scenarios enabled by AEC Data Model API.
From now you have more than enough to start experimenting with your custom workflows on your designs.
We also have a few samples with live demos and source code available for you to leverage.

[AEC Data Model Explorer source code](https://github.com/autodesk-platform-services/aps-aecdatamodel-explorer)

[AEC Data Model Code Samples source code](https://github.com/autodesk-platform-services/aps-aecdatamodel-samples)

[AEC Data Model Dashboards source code](https://github.com/autodesk-platform-services/aps-aecdatamodel-dashboards)
