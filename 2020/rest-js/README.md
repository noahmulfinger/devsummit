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

  </aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### Open Source on GitHub

Code 🎛 [github.com/Esri/arcgis-rest-js](https://github.com/Esri/arcgis-rest-js)

Doc 📚 [esri.github.io/arcgis-rest-js](https://esri.github.io/arcgis-rest-js)

<aside class="notes" data-markdown>
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
  * url; tack on f=json so that we get our json format
  * we wait for readyStateChange, and then if it's Done, we have response text 
  * and then we have to figure out how we want to handle errors.

- not great...but doable
- this is what an http request looks like in a browser with no helper APIs
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
- url; set options including method and form encoded header, encode the f=json parameters
- we get a promise back and then can extract the json from the response object
- there's some error handing
- Does the same as the previous example with the Fetch API
- better but still can be pretty cumbersome
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

</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

<p style="font-size: 400%;">💥</p>

<aside class="notes" data-markdown>
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
* handles headers including requesting images or files 
* try to separate out as many error types as possible
* auth support including tokens for federated servers, refreshing when necessary

- But we stop at the request level, we don't handle mapping, clientside analysis or the kinds of things you'd get by using the JS API
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

- kind of similar to the Python API in functionality but lacks a notebooks environment like Jupiter notebooks where you can save and rerun your scripts

* it's all about transactions with the data from the Rest API - no mapping, display capabilities, data analysis
  </aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### In the beginning...

- [ArcGIS for Developers](https://developers.arcgis.com)
- [ArcGIS Hub](https://hub.arcgis.com)
- customers!

<aside class="notes" data-markdown>

- rest-js has been around over two years now.
- Began as a collaboration between Hub and the Developer Experience team
- Hub was using Ember and experimented with open sourcing some of the wrappers they'd created for working with the Rest API and dealing with things like users, items - they found that their solution was a little too specific - difficult for users to grab and go
- Dev experience team was using Angular and a lot of the functionality we had written mirrored that of the Hub team's - but specific to Angular applications and their conventions.
- So...how to create a solution that eliminated that duplication of work, so that these helpers could be written once and work everywhere
  </aside>

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

<aside class="notes" data-markdown>
* Over the last couple years, we've seen the floodgates open not only with customer implementations but other teams at Esri
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
- This is the set of packages we have so far.
- We have the base request package that the other packages build on, auth contains different methods for handling authentication, portal is for interacting with users, groups, and items in ArcGIS Online or your enterprise portal, feature layer is for querying and editing data in a feature service layer, service-admin is for managing metadata about a service.
- Right now it only contains functionality for dealing with feature services, but its meant for things like creating a new service and adding layers or other properties to a service.
- Geocoding and routing are self-explanatory, they provide wrapper functions for using the geocoding and routing services.
- As you can see all these packages are very light-weight. They are also set up to be tree-shakeable so that if you are using a bundler like web pack you can only bundle the functions you actually use.

</aside>

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

<aside class="notes" data-markdown>
- For functions that only require one piece of information, they can take in that info directly as a single argument or as part of a larger object.
- In this case if you wanted to just do a default query of ArcGIS Online for items containing the string water, you could do the first query.
- You could alternatively pass in an object containing a query field and some other options

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

<aside class="notes" data-markdown>
- However, if more than one piece of information is required, you would always pass in an object with the required options
- For all functions, the object extends the default request options provided in the base request library, so you could pass in authentication, headers, etc to deleteFeatures as well.
- (OPEN delete features doc)
- Here’s what deleteFeatures looks like in the documentation. 
- If we scroll down to the options, we can see url and objectIds parameters which are required as well as some optional inherited parameters like authentication, which I mentioned earlier.
- For additional custom params, you can use params here which will take in any keys and values.
- This is here because goal is not to provide all the parameters available in the REST API, just the common ones 
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
- The functions in the library also try to reduce the overhead of constructing a url and require as little as possible. 
- For example, in the setItemAccess function, we only need the id of the item, the access value  or who it should be shared with, and some authentication to know if the current user is allowed to update the specified item

</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

Simplified developer experience, even when the underlying logic is [complicated](https://github.com/Esri/arcgis-rest-js/blob/869f466c47b2e80768018b20c89c6279212767aa/packages/arcgis-rest-portal/src/sharing/group-sharing.ts)

- we ensure the response is _deterministic_
- we figure out _which_ url to call (based on role)

<aside class="notes" data-markdown>
- Overall the focus is on simplifying the developer experience  even when the logic can get complicated.
- (OPEN group sharing js)
- Here’s one example, probably the most complex functionality under the hood that the library provides.
- Sharing items can require different urls depending the permissions of the user and is not always deterministic depending on what groups an item is already shared with.
- So this changeGroupSharing method handles all that and provides some better error messaging when there’s failure. 
- This is just to show some of the value added when using this library vs directly using the REST API

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
- Another important concern when working with the REST API is authentication.
- The most common method for this is through a UserSession from the auth package.
- Using either ArcGIS Online or an enterprise portal, you can construct a user session with standard username and password credentials
- This in and of itself doesnt fetch a token, it just sets up config needed for making authenticated requests
- It is similar to JS API IdentityManager in that it stores a specific set of credentials.
- However, it doesn't juggle multiple portals and doesn't present a UI to log in when an anonymous request fails.
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
- This UserSession serves as an authentication object that you’ve seen in previous code samples. This authentication object is passed in to the request.
- The first time a request is made with it, it will handle getting a token from the server, checking whether the server is trusted or federated.
- On subsequent requests using the same authentication, it will reuse the same token and handle expiration by getting a fresh token from the server.
- We will go over some more complex authentication patterns like oauth in our demos later.

</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### since DevSummit 2019...

🎉 rest-js v2.0.0! 🎉

(plus 20 additional releases 🚀)

<aside class="notes" data-markdown>
- Since the previous version of this talk in DevSummit 2019, the library has been actively developed.
- We’ve released a 2.0 version and there have been 20+ additional releases beyond that.
- So its been pretty active.
- I'm going to go over a few things that have been added in version 2

</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

### What's new in v2+

- [SearchQueryBuilder](https://esri.github.io/arcgis-rest-js/api/portal/SearchQueryBuilder/)

- Improved [paging](https://esri.github.io/arcgis-rest-js/api/portal/searchItems)

- `setDefaultRequestOptions()` and `withOptions()`

- Package and type reorganization

<aside class="notes" data-markdown>
- We added a new SearchQueryBuilder class that allows you to easily compose a search query without needing to do a bunch of manual string management. 
- We also added some improved paging functionality, we added a nextPage function returned from any search query that you can call to easily get the next page of results without having to construct a new request.
- We also added two helper functions, setDefaultRequestOptions and withOptions. setDefaultRequestOptions allows you to set custom options for all requests.
- Its useful for things like request headers that will likely need to be with every requests.
-  withOptions allows you set custom options for a specific request.
- For instance if you want to create an authentication version of a request that you can call and you don’t need to pass authentication to it every time.
- There was also a bunch of reorganization of packages, types, and functions based on the feedback and needs of contributors.
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
- So this is an example of creating a search query using the new builder class. 
- In standard queries, you would need to construct the query as a long string, but in version 2.0, you can build up a query using helper functions. 
- This is especially useful for complex queries or ones that require a lot of conditionalization.

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
- One example of the package reorganization is grouping up the separate items, users, groups, and sharing packages into a single portal package.
- These packages all shared a lot of functionality and were already very small, so grouping them up didn’t increase the size too much
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

<aside class="notes" data-markdown>
- For those using typescript, one improvement is including types in the same packages where they are used.
- Previously you would need to install a separate types package for any a lot of typescript types.
- There are additional changes in version 2, and you can check out the release notes for the full list of what’s changed.
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-2.png" -->

Check out the [release notes](https://esri.github.io/arcgis-rest-js/guides/whats-new-v2-0/) for the full list

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-4.png" -->

## Demo

### [OAuth in Browser](https://github.com/Esri/arcgis-rest-js/tree/master/demos/oauth2-browser)

- [Auth package](https://esri.github.io/arcgis-rest-js/api/auth/UserSession/)
- [rest-js via CDN](https://esri.github.io/arcgis-rest-js/guides/from-a-cdn/)

<aside class="notes" data-markdown>
* Vanilla JS implementation

- Demo follows the app login pattern, in which an app uses your client id to obtain credentials

- Demonstrate inline Sign In

- What's going on?

- Use arcgis for developers to create an app item and set a redirect url

- in app code, we set our client id

- Index.html - CDN script tags

- Index.html - line 178 our click handler. Note that when using the CDN we preface our method calls with `arcgisREST`

- Authenticate.html just calls a function to complete the OAuth process. The session info is saved in local storage
  </aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-4.png" -->

## Demo

### [React Component](https://github.com/oppoudel/react-geocoder)

- [Geocoding package](https://esri.github.io/arcgis-rest-js/api/geocoding/)
- [Downshift](https://github.com/downshift-js/downshift)

<aside class="notes" data-markdown>

- This is a React geocoding component created by a user - link to his repo is in the slides

- Running this locally to show the upgrade to rest-js 2.9

- This is built with React and Downshift, which is a project to create low-level accesible dropdowns, menus and other components in React

- Demonstrate component in browser

- In Geocoder.js,

  - If the menu is open, Geocode component calls the `suggest` method from the geocoding package

  - debounce - prevents an API call with every key stroke and improves performance

  - Suggest provides magicKey values in addition to text result. The key links a suggestion to an address or place. This can be passed to the geocode call to improve search time. Note that magic Keys are not permanent across versions of the World Geocoding search and thus shouldn't be stored by a client application, but instead only used as a parameter for the geocode call.

  - handleStateChange is passed to the Downshift instance so that onStateChange, the function is called, which in turn calls the geocode method with the magicKey passed as a parameter

</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-4.png" -->

## Demo

### [JS API Integration with Angular](https://angular-js-api-integration-demo.stackblitz.io/)

<aside class="notes" data-markdown>
- For the first demo, I'm going to show an example of using both the JS API and rest JS together in an Angular application
- Sign in. Mention oauth2 sign in setup 
- Shows list of feature services owned by the user
- If I click one one, it adds it to the map, then zooms in to the map to view the layer
- As you can see, the user is automatically signed in to the JS API after signing in using rest JS
- Switch maps
- Sign out
- Go to code
- Show session service
- Sign in happens via ouath with beginOAuth2
- Sign in completes with completeOAuth2 and then the session is stored both in local storage and in this session store.
- This simple store is set up so we can easily share the session across multiple components
- Go to item-list component
- The component subscribes to the session service to always get the latest session value.
- If we have a session, we construct a query using the search query builder for all Feature Services where the owner matches the current user
- Then we do a search and display the results to the user.
- Go to map panel component
- The first thing I'll mention is that this is using a package called esri-loader to load in JS API modules
- It automatically adds the JS API script tag whenever you need t load a module, so you don't need to include a script tag on the page and its easier to use use the modules within a framework like Angular
- This component also subscribes to the session service to get the latest session. 
- It loads the JS API identity manager via the esri-loader
- If there is a session, then it calls toCredential to return the session in the format needed for the JS API identity manager
- The alternate method fromCredential also exists, so that you can convert a JS API credential into a rest JS user session
- Once we register the token, we can add the private feature layers to the map
- In addFeatureLayer, we load the feature layer module and and then add the selected layer to the map
- Note that it imports the typescript interface IItem from the rest JS portal package.
- Once the layer has been added, we zoom to the layer so we can see it
</aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-4.png" -->

## Demo

### [Node.js](https://github.com/Esri/arcgis-rest-js/tree/master/demos/node-cli-item-management/)

<aside class="notes" data-markdown>
- So first we import the rest js packages we need since the packages are exported with both browser and node versions.
- At a higher level, we prompt the user for authentication, do a search for items based on their input, and then optionally delete each item
- In the authentication step, we prompt for the users credentials using the prompts package, which makes it easy to set up command line prompts through node.
- Then we make a call to get user which will guarantee that a token is generated because a token won't be created until you make the first request
- Then we just return the session because we will use it for all the subsequent requests
- In the searchForItems function, we prompt the user for required information and then we construct a search query
- Once the query is constructed, we make a call to searchItem passing in the authentication, query, and the number of items requested
- In the deleteItems step, we iterate through all of the items using an async iterator. Using an async iterator because its a set of promises that need to happen in sequence
</aside>

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

<aside class="notes" data-markdown>

- There are lots of great demos in the rest-js repo and beyound, we've pointed out a few here

- Reiterate that rest-js is open source and we welcome PRs, feedback, information about how you're implementing this in your projects

- Thank you!
  </aside>

---

<!-- .slide: data-background="../../template/img/2020/devsummit/bg-3.png" -->

<img src="../../template/img/esri-science-logo-white.png" class="plain" style="background: none;" />
