<!-- .slide: data-background-size="cover" style="padding-left: 80px" data-background="../../template/img/2020/devsummit/bg-1.png" -->

<h1 style="text-align: left; font-size: 80px; ">Node.js and browser applications with ArcGIS REST JS</h1>
<p style="text-align: left; font-size: 30px;">Allison Davis <a href="https://github.com/araedavis">@araedavis</a></p>
<p style="text-align: left; font-size: 30px;">Noah Mulfinger <a href="https://github.com/noahmulfinger">@noahmulfinger</a></p>
<p style="text-align: left; font-size: 30px;">slides: <a href="https://bit.ly/3aLDzaz">bit.ly/3aLDzaz</a></p>

<!-- Add these rows to push your text up so it is not interfering with the event name. Test on your actual projector! -->
<p>&nbsp;</p>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

## Agenda

1. 🌐- What is ArcGIS REST JS? Why?
1. 👩‍🚀- Who is using it? For what?
1. 📆- What's new in 2020?
1. 💯- Common Patterns
1. 🤹‍- Demos/code (to learn how it works)

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### Open Source on GitHub

Code 🎛 [github.com/Esri/arcgis-rest-js](https://github.com/Esri/arcgis-rest-js)

Doc 📚 [esri.github.io/arcgis-rest-js](https://esri.github.io/arcgis-rest-js)

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

@esri/arcgis-rest-js helps you talk

to ArcGIS Online and Enterprise

from modern browsers and Node.js.

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

Vanilla [`XMLHttpRequest`](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)

```js
// construct the url yourself and don't forget to tack on f=json
const url = "https://www.arcgis.com/sharing/rest/community/users/dmfenton";

url += "?f=json";

var xhr = new XMLHttpRequest();
xhr.onreadystatechange = function() {
  if (xhr.readyState == XMLHttpRequest.DONE) {
    // make sure JSON response doesnt indicate an error
    if (!xhr.responseText.error) {
      xhr.responseText; // { firstName: "Daniel", description: "open source geodev" ... }
    }
  }
};
xhr.open("GET", url, true);
xhr.send(null);
```

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

Vanilla [`Fetch`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)

```js
const url = "https://www.arcgis.com/sharing/rest/community/users/dmfenton";

fetch(url, {
  method: "POST", // set the request type
  headers: {
    "Content-Type": "application/x-www-form-urlencoded" // append the right header
  },
  // concat and encode parameters, append f=json
  body: encodeURIComponent("f=json")
})
  .then(response => {
    if (response.ok) {
      return response.json();
    } // dig out the json
  })
  .then(response => {
    // trap for errors inside a 200 response
    if (!response.error) {
      return response;
    }
  });
```

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

## Even more complexity

- What are all the error codes?
- How do you handle auth?
- How are dates supposed to be encoded?
- Proper encoding for objects?
- How do you manage tokens for federated servers?
- Refreshing authentication when necessary?

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

<p style="font-size: 400%;">💥</p>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

`@esri/arcgis-rest-js`

```js
import { request } from "@esri/arcgis-rest-request";

request(url)
  .then(response) // { firstName: "Daniel", description: "open source geodev" ... }
  .catch((error => {
    if(err.name === "ArcGISAuthError"){
      // handle an auth error
    } else {
      // handle a regular error
    }
  })
```

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### Value adds

- appends `f=json` and request headers
- encodes query string parameters
- creates `FormData` (when required)
- clear and informative error handling
- proper parameter encoding
- support for authentication
- ~~display a map~~
- ~~clientside analysis~~

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

`request` only expects a url, but it exposes `requestOptions` too.

```js
// url, IRequestOptions
request(url, {
  params: {
    // anything you want to pass
    foo: true,
    bar: "baz",
    more: File(),
    num: 999,
    when: Date().now() // etc.
  }
  // httpMethod: "GET",
  // authentication
  // portal,
  // headers,
  // fetch
});
```

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

the rest of the API builds on top of `request`

```js
import { geocode } from "@esri/arcgis-rest-geocoding";

// assumes you want to use ArcGIS Online
geocode("LAX").then(response); // { ... candidates: [] }

// IRequestOptions is still available
geocode({
  singleLine: "LAX",
  params: {
    forStorage: true
  },
  authentication
});
```

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### Goals

- Node.js and (modern) browsers
- a la carte / svelte
- framework agnostic
- shave down the sharp edges
- align with JS ecosystem

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### Disclaimer\*

- not a product, no roadmap
- work [in progress](https://developers.arcgis.com/rest/)
- scratching our own itch

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### Comparison

- _kind of_ analogous to ArcGIS API for Python
- **much different** than the ArcGIS API for JavaScript

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### In the beginning...

- [ArcGIS for Developers](https://developers.arcgis.com)
- [ArcGIS Hub](https://hub.arcgis.com)
- customers!

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### As of 2020

- ArcGIS Hub
- ArcGIS for Developers
- Storymaps
- Web AppBuilder (next generation)
- ArcGIS Solutions
- ArcGIS Enterprise
- ArcGIS Analytics for IoT
- Esri UK
- Startups / Partners
- Customers
- You?

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

## packages 📦!

- `request` 2.8 kB
- `auth` 3.6 kB
- `portal` 5.1 kB
- `feature-layer` 1.3 kB
- `service-admin` 746 B
- `geocoding` 1 kB
- `routing` 642 B

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-3.png" -->

## Common Patterns

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

when only **one** piece of information is required

```js
import { searchItems } from "@esri/arcgis-rest-portal";

searchItems("water").then(response); // { total: 355, etc... }
// or
searchItems({
  query: "water",
  httpMethod: "GET",
  authentication
});
```

you can pass in it in directly.

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### if **more** than one piece of information is needed

<pre style="width: 50%; margin: 0 auto; box-shadow: none;">
<code class="hljs js">deleteFeatures({
  url: "https://server.arcgis.com/arcgis/rest/services/MyData/FeatureServer/0"
  objectIds: [ 123 ]
})
  .then(response)
</code>
</pre>

<pre style="width: 50%; margin: 0 auto; box-shadow: none;">
<code class="hljs json">{
  // response
  "deleteResults": [
    {
      "objectId": 123,
      "success": true
    }
  ]
}
</code>
</pre>

only an object can be passed in, [_extends_](https://esri.github.io/arcgis-rest-js/api/feature-layer/deleteFeatures/) `IRequestOptions`

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### update who can access an item

```js
import { setItemAccess } from "@esri/arcgis-rest-sharing";

setItemAccess({
  id: `fe8`, // which item?
  access: `public`, // who should be able to see it?
  authentication // user allowed to update
}).then(response);
```

[`ISetItemAccessOptions`](https://esri.github.io/arcgis-rest-js/api/portal/setItemAccess/)

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

Simplified developer experience, even when the underlying logic is [complicated](https://github.com/Esri/arcgis-rest-js/blob/869f466c47b2e80768018b20c89c6279212767aa/packages/arcgis-rest-portal/src/sharing/group-sharing.ts)

- we ensure the response is _deterministic_
- we figure out _which_ url to call (based on role)

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

## Authentication

```js
import { UserSession } from "@esri/arcgis-rest-auth";

// ArcGIS Online credentials
const authentication = new UserSession({ username, password });

// ArcGIS Enterprise credentials
const enterpriseAuth = new UserSession({
  username,
  password,
  portal: `https://gis.city.gov/sharing/rest`
});
```

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

`UserSession` keeps track of token expiration

```js
const url = `http://geocode.arcgis.com/arcgis/rest/services/World/GeocodeServer/`;

const authentication = new UserSession({ username, password });

request(url, { authentication }).then(response => {
  // the same token will be reused for the second request
  request(url, { authentication });
});
```

and whether or not a server is trusted (federated)

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### since DevSummit 2019...

🎉 rest-js v2.0.0! 🎉

(plus 20 additional releases 🚀)

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### What's new in v2+

- [SearchQueryBuilder](https://esri.github.io/arcgis-rest-js/api/portal/SearchQueryBuilder/)

- Improved [paging](https://esri.github.io/arcgis-rest-js/api/portal/searchItems)

- `setDefaultRequestOptions()` and `withOptions()`

- Package and type reorganization

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### Building search queries

```js
// 1.x
const q =
  "Trees AND owner: US Forest Service AND (type: 'Web Mapping Application' OR type: 'Mobile Application')";

// 2.x
const q = new SearchQueryBuilder()
  .match("Trees")
  .and()
  .match("US Forest Service")
  .in("owner")
  .and()
  .startGroup()
  .match("Web Mapping Application")
  .in("type")
  .or()
  .match("Mobile Application")
  .in("type")
  .endGroup();
```

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### One portal package to rule them all

```bash
// 1.x
npm install @esri/arcgis-rest-items &&
@esri/arcgis-rest-users &&
@esri/arcgis-rest-groups &&
@esri/arcgis-rest-sharing

// 2.x
npm install @esri/arcgis-rest-portal

```

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### Packages install types automatically

```typescript
// 1.x
import { IPoint } from "@esri/arcgis-rest-common-types";
import { reverseGeocode } from "@esri/arcgis-rest-geocoder";

reverseGeocode({ x: 34, y: -118 } as IPoint);

// 2.x
import { IPoint, reverseGeocode } from "@esri/arcgis-rest-geocoding";

reverseGeocode({ x: 34, y: -118 } as IPoint);
```

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

Check out the [release notes](https://esri.github.io/arcgis-rest-js/guides/whats-new-v2-0/) for the full list

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-4.png" -->

## Demo

### [OAuth in Browser](https://github.com/Esri/arcgis-rest-js/tree/master/demos/oauth2-browser)

- [Auth package](https://esri.github.io/arcgis-rest-js/api/auth/UserSession/)
- [rest-js via CDN](https://esri.github.io/arcgis-rest-js/guides/from-a-cdn/)

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-4.png" -->

## Demo

### [React Component](https://github.com/oppoudel/react-geocoder)

- [Geocoding package](https://esri.github.io/arcgis-rest-js/api/geocoding/)
- [Downshift](https://github.com/downshift-js/downshift)

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-4.png" -->

## Demo

### [JS API Integration with Angular](https://github.com/noahmulfinger/angular-js-api-integration-demo)

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-4.png" -->

## Demo

### [Node.js](https://github.com/Esri/arcgis-rest-js/tree/master/demos/node-cli-item-management/)

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-3.png" -->

### Resources 📚

- [Link to slides](https://bit.ly/3aLDzaz)
- [GitHub repo](https://github.com/Esri/arcgis-rest-js)
- [Docs site](https://esri.github.io/arcgis-rest-js/)
- [Demo at Observables](https://beta.observablehq.com/@jgravois/introduction-to-esri-arcgis-rest-js)
  <p>&nbsp;</p>

### More Demos 💻

- [Sapper](https://github.com/Esri/arcgis-rest-js/tree/master/demos/webmap-checker-sapper)
- [Web Components with Stencil](https://github.com/esridc/hub-components)
- [Lamda Functions](https://medium.com/@adamjpfister/know-your-apis-6dc6ea3d084c)

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-3.png" -->

<img src="../../template/img/esri-science-logo-white.png" class="plain" style="background: none;" />
