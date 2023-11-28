---
layout: page
title: Connecting with Viewer and Advanced Queries
nav_order: 4
has_children: false
permalink: /connection/home/
---

# Connecting with Viewer and Advanced Queries

In this section, we'll understand how the explorer connects the elements retrieved from the response with the elements rendered with the Viewer.
After this, we'll explore more complex workflows supported by then AEC Data Model API.
Let's begin with Viewer.

## Connecting with Viewer

Every time you run a query against a specific design and the same design is rendered in the Viewer, the elements from the response are isolated in the Viewer scene.
To understand the process, let's analyze the diagram below:

![Viewer Connection Process](../../assets/images/viewerconnectionprocess.png)
![Viewer Connection Process](../assets/images/viewerconnectionprocess.png)

1. It starts when the user push the button to run a query.
2. In case there's a design rendered in viewer, a method gets triggered when the response is received, just like in the snippet below:

```js
return fetch("https://developer.api.autodesk.com/aecdatamodel/graphql", {
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
    globalViewer.getExtension("AIMFilterExtension").filterModel();
  }
  return queryResponse;
});
```

3. The elements are extracted from the AEC Data Model API response. Apart from the id we can see from the response, every element has an persistent id that remains the same in Revit, Viewer and AEC Data Model. For those familiar with Revit, this is the [UniqueId](https://www.revitapidocs.com/2024/f9a9cb77-6913-6d41-ecf5-4398a24e8ff8.htm). In the response this id is available in the **External Id** property. So we just need to retrieve every value for this property from the response, and the explorer does that using regular expressions through the snippet below:

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

4. The list of ids is passed to Viewer. From Viewer's perspective, these values are available also as the **external id** property from each object. Since the Viewer uses the **dbId** in its methods, we can use the [getExternalIdMapping](https://aps.autodesk.com/en/docs/viewer/v7/reference/Viewing/Model/#getexternalidmapping-onsuccesscallback-onerrorcallback) function to retrieve the correlation between **dbIds** and **external ids**.

> We have [this blog](https://aps.autodesk.com/blog/get-dbid-externalid) on external ids in Viewer. Feel free to check it out ;)

5. With the list of **dbIds**, we can simply use Viewer's [isolate](https://aps.autodesk.com/en/docs/viewer/v7/reference/Viewing/Model/#getexternalidmapping-onsuccesscallback-onerrorcallback) and [fitToView](https://aps.autodesk.com/en/docs/viewer/v7/reference/Viewing/GuiViewer3D/#fittoview-objectids-model-immediate) methods to adjust the scene to focus in our elements.

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

And now you know what happens behind the scene to filter elements from the response in Viewer. Now we can focus in additional queries.

## Advanced Queries

[Next Step - Additional Workflows]({{ site.baseurl }}/workflows/home/){: .btn}
