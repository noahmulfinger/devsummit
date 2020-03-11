<!-- .slide: data-background-size="cover" style="padding-left: 80px" data-background="../../template/img/2020/devsummit/bg-1.png" -->

<h1 style="text-align: left; font-size: 80px; ">Node.js and browser applications with ArcGIS REST JS</h1>
<p style="text-align: left; font-size: 30px;">Allison Davis <a href="https://github.com/araedavis">@araedavis</a></p>
<p style="text-align: left; font-size: 30px;">Noah Mulfinger <a href="https://github.com/noahmulfinger">@noahmulfinger</a></p>
<p style="text-align: left; font-size: 30px;">slides: <a href="http://bit.ly/abc123"><code>TODO ADD URL</code></a></p>

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

<aside class="notes" data-markdown>

* Introduction/What is ArcGIS REST JS? Why? (Allison) 8 min
* Who is using it? For what? (Allison) 2 min
* Packages (Noah) 2 min
* Common Patterns (Noah) 5 min
* What's new in 2020? (Noah) 5 min
* Auth/CDN demo (Allison) 5 min
* React demo (Allison) 5 min
* JS API integration demo (Noah) 5 min
* node cli demo (Noah) 5 min
* Resources and Wrap-up 3 min (Allison)
* Questions 10 min
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### Open Source on GitHub

Code 🎛 [github.com/Esri/arcgis-rest-js](https://github.com/Esri/arcgis-rest-js)

Doc 📚 [esri.github.io/arcgis-rest-js](https://esri.github.io/arcgis-rest-js)

<aside class="notes" data-markdown>
(Allison)
  * Both the rest-js codebase and docs are open source
  *  API reference is generated from comments within the code using TypeDoc
  * Guides and demos
  * pull requests (suggestions, improvements) welcome and appreciated
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

@esri/arcgis-rest-js helps you talk

to ArcGIS Online and Enterprise

from modern browsers and Node.js.

<aside class="notes" data-markdown>
(Allison)
We try to handle as many common use cases and as many idiosyncracies of the REST API as possible
</aside>

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

<aside class="notes" data-markdown>
(Allison)
  * url; tack on f=json so that we get our json format
  * we wait for readyStateChange, and then if it's Done, we have response text 
  * and then we have to figure out how we want to handle errors.

  * not great...but doable
  * this is what an http request looks like in a browser with no helper APIs
</aside>

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

<aside class="notes" data-markdown>
  Allison

  * url; set options including method and form encoded header, encode the f=json parameters
  * we get a promise back and then can extract the json from the response object
  * there's some error handing
  * Does the same as the previous example with the Fetch API 
  * better but still can be pretty cumbersome
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

## Even more complexity

- What are all the error codes?
- How do you handle auth?
- How are dates supposed to be encoded?
- Proper encoding for objects?
- How do you manage tokens for federated servers?
- Refreshing authentication when necessary?

<aside class="notes">
Allison

</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

<p style="font-size: 400%;">💥</p>

<aside class="notes" data-markdown>
Allison
Rest-js tries to handle as much of this as possible so your head doesn't explode
</aside>

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

<aside class="notes" data-markdown>
(Allison)
  does more in fewer lines of code 
</aside>

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

<aside class="notes" data-markdown>
(Allison)
* handles headers including requesting images or files 
* try to separate out as many error types as possible
* auth support including tokens for federated servers, refreshing when necessary

* But we stop at the request level, we don't handle mapping, clientside analysis or the kinds of things you'd get by using the JS API
</aside>

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

<aside class="notes" data-markdown>
Allison
  * httpMethod defaults to POST
  * IRequestOptions give you more control over the request
  * authentication helps you generate tokens when you can't make an anonymous request
  * a custom Fetch implementation can be passed in too
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

the rest of the API builds on top of `request`

```js
import { geocode } from "@esri/arcgis-rest-geocoding";

// assumes you want to use ArcGIS Online
geocode("LAX").then(response); // { ... candidates: [] }

// IRequestOptions is still available
geocode("LAX", {
  params: {
    forStorage: true
  },
  authentication
});
```

<aside class="notes" data-markdown>
  Allison
  * Under the hood, geocoding just calls request
  * But with nicer syntax 
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### Goals

- Node.js and (modern) browsers
- a la carte / svelte
- framework agnostic
- shave down the sharp edges
- align with JS ecosystem

<aside class="notes" data-markdown>
Allison
* work in node and modern browsers with small set of polyfills
* keeping the library as small as possible for best loadtime 
* framework agnostic - so that you can use rest-js with React, Angular, Vue, vanilla JS
* keep the rough edges away from your application code; handle edge cases and such from the rest api so you don't have to
* align with the rest of the JS ecosystem - whatever your tooling, your bundler, frameworks - without having to use additional plugins or config

</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### Disclaimer\*

- not a product, no roadmap
- work [in progress](https://developers.arcgis.com/rest/)
- scratching our own itch

<aside class="notes" data-markdown>
Allison
* not an official product
* started as a way to standardize functionality and utilities that different Esri teams had created 
* that's why it was decided to open source it - if Esri teams are getting and adding so much value, certainly users and partners can too
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### Comparison

- _kind of_ analogous to ArcGIS API for Python
- **much different** than the ArcGIS API for JavaScript

<aside class="notes" data-markdown>
Allison
  * kind of similar to the Python API in functionality but lacks a notebooks environment like Jupiter notebooks where you can save and rerun your scripts

  * it's all about transactions with the data from the Rest API - no mapping, display capabilities, data analysis
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### In the beginning...

- [ArcGIS for Developers](https://developers.arcgis.com)
- [ArcGIS Hub](https://hub.arcgis.com)
- customers!

<aside class="notes" data-markdown>
Allison
* rest-js has been around over two years now. 
* Began as a collaboration between Hub and the Developer Experience team 
* Hub was using Ember and experimented with open sourcing some of the wrappers they'd created for working with the Rest API and dealing with things like users, items - they found that their solution was a little too specific - difficult for users to grab and go
* Dev experience team was using Angular and a lot of the functionality we had written mirrored that of the Hub team's - but specific to Angular applications and their conventions. 
* So...how to create a solution that eliminated that duplication of work, so that these helpers could be written once and work everywhere
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### As of 2020

- ArcGIS Hub
- ArcGIS for Developers
- Storymaps
- Web AppBuilder (next generation)
- ArcGIS Urban
- Professional Services
- ArcGIS Solutions
- ArcGIS Enterprise 
- ArcGIS Analytics for IoT
- Esri UK
- Startups / Partners
- Customers
- You?

<aside class="notes" data-markdown>
Allison
* Over the last couple years, we've seen the floodgates open not only with customer implementations but other teams at Esri

* Handoff to Noah
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

## packages 📦!

- `request`
- `auth`
- `portal`
- `feature-layer`
- `service-admin`
- `geocoding`
- `routing`

<aside class="notes">
Noah
Took package sizes out for now, although we can always add them back in later? 
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### since DevSummit 2019...

🎉 rest-js v2.0.0! 🎉

(plus 20 additional releases 🚀)

<aside class="notes">
Noah
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

## What's new in v2+ 

### Parameters and Query Improvements
- [SearchQueryBuilder](https://esri.github.io/arcgis-rest-js/api/portal/SearchQueryBuilder/) 

- Improved [paging](https://esri.github.io/arcgis-rest-js/api/portal/searchItems/#nextPage)

- Reusing parameters with `setDefaultRequestOptions()` and `withOptions()`

<aside class="notes">
Noah
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

## What's new in v2+, continued

### One portal package to rule them all: 

```bash
// 1.x
npm install @esri/arcgis-rest-items &&
@esri/arcgis-rest-users &&
@esri/arcgis-rest-groups &&
@esri/arcgis-rest-sharing

// 2.x
npm install @esri/arcgis-rest-portal

```

Minor renaming and reorganization of some methods: check out the [release notes](https://esri.github.io/arcgis-rest-js/guides/whats-new-v2-0/) for the full list

<aside class="notes">
Noah
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

## What's new in v2+, continued

### Packages install typings automatically 

```typescript
// 1.x
import { IPoint } from "@esri/arcgis-rest-common-types";
import { reverseGeocode } from "@esri/arcgis-rest-geocoder";

reverseGeocode({ x: 34, y: -118} as IPoint);

// 2.x
import { IPoint, reverseGeocode } from "@esri/arcgis-rest-geocoding";

reverseGeocode({ x: 34, y: -118} as IPoint);
```

<aside class="notes">
Noah
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

when only **one** piece of information is required

```js
import { getLayer } from "@esri/arcgis-rest-feature-service";

const url = `http://services.arcgis.com/.../SF311/FeatureServer/0/deleteFeatures`;

getLayer(url).then(response); // { name: "311", id: 0, ... }

// or
getLayer(url, {
  httpMethod: "GET",
  authentication // etc.
});
```

[IRequestOptions](https://esri.github.io/arcgis-rest-js/api/feature-service/getLayer/) still plays second 🎻
.

<aside class="notes">
Noah
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### if **more** than one piece of information is needed

<div class="container">

<div class="col">
  <pre style="width: 500px; margin: 0 auto; box-shadow: none;"><code class="hljs js" style="height: 400px;">
deleteFeatures({
  url,
  objectIds: [ 123 ]
})
  .then(response)
    </code>
    </pre>
</div>

<div class="col">
  <pre style="width: 500px; margin: 0 auto; box-shadow: none;"><code class="hljs json" style="height: 400px;">
{
  "deleteResults": [
    {
      "objectId": 123,
      "success": true
    }
  ]
}
</code></pre>
</div>
</div>
only one parameter is exposed and we [_extend_](https://esri.github.io/arcgis-rest-js/api/feature-service/deleteFeatures/) `IRequestOptions`
<aside class="notes">
Noah
show that this method expects ISetAccessRequestOptions
this gives a higher level abstraction than just { params }
admit that required parameters are obscured by optional (and inherited ones)
remind folks that the code snippet is good enough for most
and that the structure is mostly for TypeScript consumers
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### update who can access an [item](http://edn.maps.arcgis.com/home/item.html?id=d9af3e31a562431988666e86bfc8a0d5)

```js
import { setItemAccess } from "@esri/arcgis-rest-sharing";

setItemAccess({
  id: `fe8`, // which item?
  access: `public`, // who should be able to see it?
  authentication // user allowed to update
}).then(response);
```

[`ISetItemAccessOptions`](https://esri.github.io/arcgis-rest-js/api/sharing/setItemAccess/)

<aside class="notes">
Noah
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

The DX is simple, even when the underlying logic is [complicated](https://github.com/Esri/arcgis-rest-js/blob/master/packages/arcgis-rest-sharing/src/group-sharing.ts#L74-L161)

- we ensure the response is \_deterministic
- we figure out _which_ url to call (based on role)

<aside class="notes">
Noah
</aside>

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

<aside class="notes">
Noah
  this in and of itself doesnt fetch a token
  similar to JSAPI IdentityManager
    * DOESNT juggle multiple portals
    * doesnt present a UI to login when an anonymous request fails

tokens arent fetched until its time to make a request

</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

`UserSession` keeps track of token expiration

```js
const url = `http://geocode.arcgis.com/arcgis/rest/services/World/GeocodeServer/`;

request(url, { authentication }).then(response => {
  // the same token will be reused for the second request
  request(url, { authentication });
});
```

and whether or not a server is trusted (federated)

<aside class="notes">
Noah
  the session keeps track of the expiration of tokens and trusted servers
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

```js
const url = `http://geocode.arcgis.com/arcgis/rest/services/World/GeocodeServer/`;

request(url, { authentication }).then(response => {
  // reuse the same token since it hasn't expired
  request(url, { authentication });
});
```

<aside class="notes">
Noah
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-4.png" -->

## Demo

### [OAuth in Browser](https://github.com/Esri/arcgis-rest-js/tree/master/demos/oauth2-browser)

- [Auth package](https://esri.github.io/arcgis-rest-js/api/auth/UserSession/)
- [rest-js via CDN](https://esri.github.io/arcgis-rest-js/guides/from-a-cdn/)

<aside class="notes" data-markdown>
Allison
* Vanilla JS implementation 

* Demo follows the app login pattern, in which an app uses your client id to obtain credentials

* Demonstrate inline Sign In 

* What's going on? 

* Use arcgis for developers to create an app item and set a redirect url

* in app code, we set our client id

* Index.html - CDN script tags

* Index.html - line 178 our click handler. Note that when using the CDN we preface our method calls with `arcgisREST`

* Authenticate.html just calls a function to complete the OAuth process. The session info is saved in local storage
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-4.png" -->

## Demo

### [React Component](https://github.com/oppoudel/react-geocoder)

- [Geocoding package](https://esri.github.io/arcgis-rest-js/api/geocoding/)
- [Downshift](https://github.com/downshift-js/downshift)

<aside class="notes" data-markdown>
Allison

* This is a React geocoding component created by a user - link to his repo is in the slides

* Running this locally to show the upgrade to rest-js 2.9

* This is built with React and Downshift, which is a project to create low-level accesible dropdowns, menus and other components in React

* Demonstrate component in browser

* In Geocoder.js, 
  * If the menu is open, Geocode component calls the `suggest` method from the geocoding package

  * debounce - prevents an API call with every key stroke and improves performance

  * handleStateChange is passed to the Downshift instance so that onStateChange, the function is called, which in turn calls the geocode method

  * MagicKey from the suggestions is what's passed to the geocode call (point that out in the rest-js docs)

</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-4.png" -->

## Demo

### [JS API Integration]()

<aside class="notes">
Noah
@TODO create JS API integration demo
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-4.png" -->

## Demo

### [Node.js](https://github.com/Esri/arcgis-rest-js/tree/master/demos/node-cli-item-management/)

<aside class="notes">
  Noah

  demo (and API functionality) came from a user
  admit that we should be hosting live demos but for now you have to run them yourself.
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-3.png" -->

### Resources 📚

- [Link to slides]()
- [GitHub repo](https://github.com/Esri/arcgis-rest-js)
- [Docs site](https://esri.github.io/)
- [Demo at Observables](https://beta.observablehq.com/@jgravois/introduction-to-esri-arcgis-rest-js)
<p>&nbsp;</p> 

### More Demos 💻

- [Sapper](https://github.com/Esri/arcgis-rest-js/tree/master/demos/feature-collection-manager-sapper)
- [Web Components with Stencil](https://github.com/esridc/hub-components)
- [Lamda Functions](https://medium.com/@adamjpfister/know-your-apis-6dc6ea3d084c)

<aside class="notes" data-markdown>
Allison

* There are lots of great demos in the rest-js repo and beyound, we've pointed out a few here

* Reiterate that rest-js is open source and we welcome PRs, feedback, information about how you're implementing this in your projects

* Thank you!
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-3.png" -->

<img src="../../template/img/esri-science-logo-white.png" class="plain" style="background: none;" />
