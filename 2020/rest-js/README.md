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
geocode({
  singleLine: "LAX",
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

- `request` 2.8 kB
- `auth` 3.6 kB
- `portal` 5.1 kB
- `feature-layer` 1.3 kB
- `service-admin` 746 B
- `geocoding` 1 kB
- `routing` 642 B

<aside class="notes" data-markdown>
Noah
- set of packages we have so far
- wrappers on top of request package
- all very lightweight
- if you're bundling them they would be tree-shakeable, only including the functions you actually need
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### since DevSummit 2019...

🎉 rest-js v2.0.0! 🎉

(plus 20 additional releases 🚀)

<aside class="notes" data-markdown>
Noah
- had a major release middle of last year
- lots of updates throughout the year, bug fixes and feature improvements
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### What's new in v2+

- [SearchQueryBuilder](https://esri.github.io/arcgis-rest-js/api/portal/SearchQueryBuilder/)

- Improved [paging](https://esri.github.io/arcgis-rest-js/api/portal/searchItems/#nextPage)

- `setDefaultRequestOptions()` and `withOptions()`

- Package and type reorganization

<aside class="notes" data-markdown>
Noah
- Added a way to build search queries, easier than appending to a long string
- easy paging, making it easier to get the next page of results
- helper methods for reducing repetition of common request options
- lot of reorganization of packages, types, and functions based on feedback and needs of contributors
</aside>

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

<aside class="notes" data-markdown>
Noah
- reduce the number of packages
- overzealous in splitting up the functionality
- shared functionality 
</aside>

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

<aside class="notes" data-markdown>
Noah
- searchquerybuilder allows makes queries simpler, less error prone
- a bit more code, but really useful for complex queries
- don't need to manually manage a string
</aside>

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

Check out the [release notes](https://esri.github.io/arcgis-rest-js/guides/whats-new-v2-0/) for the full list

<aside class="notes" data-markdown>
Noah
- final 2.x update I'll mention
- used to need a separate package for typescript types
- now they are included automatically for you
- For all info, see the release notes
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-3.png" -->

## Common Patterns

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

when only **one** piece of information is required

```js
import { searchItems } from "@esri/arcgis-rest-portal";
//
searchItems('water')
  .then(response) // { total: 355, etc... }
// or
searchItems({
  query: "water",
  httpMethod: "GET",
  authentication
});
```

you can pass in it in directly.

<aside class="notes" data-markdown>
Noah
- When a single piece of info is required, you can pass in the info directly
- If you want to pass in additional properties, you pass in an object that extends IRequestOptions
</aside>

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
  "deleteResults": [
    {
      "objectId": 123,
      "success": true
    }
  ]
}
</code>
</pre>

only an object can be passed in, [_extends_](https://esri.github.io/arcgis-rest-js/api/feature-service/deleteFeatures/) `IRequestOptions`

<aside class="notes" data-markdown>
Noah
- with multiple required params, an object must be passed in 
- extends IRequestOptions as mentioned earlier
- go to deleteFeatures page on doc site and show options
- mention `params` object for additional custom params
  - goal is not to document the whole rest api, just common options
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

[`ISetItemAccessOptions`](https://esri.github.io/arcgis-rest-js/api/portal/setItemAccess/)

<aside class="notes" data-markdown>
Noah
- reduce overhead of constructing url for an item
- only the bare minimum required info is needed.
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

Simplified developer experience, even when the underlying logic is [complicated](https://github.com/Esri/arcgis-rest-js/blob/master/packages/arcgis-rest-portal/src/sharing/group-sharing.ts#L76-L173)

- we ensure the response is \_deterministic
- we figure out _which_ url to call (based on role)

<aside class="notes" data-markdown>
Noah
- overall simplifying the developer experience of interacting with the REST API
- do a quick view of the changeGroupSharing to show how complicated it is
- requirement of the hub team that often has to deal with sharing
- 
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

<aside class="notes" data-markdown>
Noah
- UserSession is the most common way to handle authentication
- this in and of itself doesnt fetch a token, just sets up config needed for making authenticated requests
- similar to JS API IdentityManager
    - doesn't juggle multiple portals
    - doesn't present a UI to login when an anonymous request fails
- tokens arent fetched until its time to make a request
</aside>

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

<aside class="notes" data-markdown>
Noah
- the UserSession keeps track of the expiration of tokens and trusted servers
- pass around the authentication object
- UserSession will handle getting new tokens if they get expired
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

  * Suggest provides magicKey values in addition to text result. The key links a suggestion to an address or place. This can be passed to the geocode call to improve search time. Note that magic Keys are not permanent across versions of the World Geocoding search and thus shouldn't be stored by a client application, but instead only used as a parameter for the geocode call.  

  * handleStateChange is passed to the Downshift instance so that onStateChange, the function is called, which in turn calls the geocode method with the magicKey passed as a parameter


</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-4.png" -->

## Demo

### [JS API Integration](https://angular-js-api-integration-demo.stackblitz.io/)

<aside class="notes" data-markdown>
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

- [Link to slides](https://bit.ly/3aLDzaz)
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
