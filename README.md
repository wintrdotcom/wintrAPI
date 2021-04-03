# WINTR API

[![WINTR Web scraping API](https://www.wintr.com/app/template/favicon.png)](https://www.wintr.com)

> **WINTR is a free and easy to use web scraping API that allows you to scrape any webpage and to parse its HTML content without dealing with proxies, IP rotation, headless browsers, and captchas. Create an account [in here](https://www.wintr.com) to get started**

### Table of Contents

  - [Features](#features)
  - [Options](#options)
  - [Examples](#examples)
    - [Scrape a webpage](#scrape-a-webpage)
    - [Geo-located request](#geo-located-request)
    - [Custom referer](#custom-referer)
    - [Custom useragent](#custom-useragent)
    - [Custom headers](#custom-headers)
    - [Custom method](#custom-method)
    - [HTTP authentication](#http-authentication)
    - [Scrape Javascript rendered webpage](#scrape-javascript-rendered-webpage)
    - [Scrape Javascript rendered webpage rendering only the DOM](#scrape-javascript-rendered-webpage-rendering-only-the-dom)
    - [Scrape Javascript rendered webpage including external assets](#scrape-javascript-rendered-webpage-including-external-assets)
    - [Scrape Javascript rendered webpage & wait 5 seconds](#scrape-javascript-rendered-webpage--wait-5-seconds)
    - [Scrape Javascript rendered webpage & wait for element to load](#scrape-javascript-rendered-webpage--wait-for-element-to-load)
    - [Scrape Javascript rendered webpage with custom cookies](#scrape-javascript-rendered-webpage-with-custom-cookies)
    - [Scrape Javascript rendered webpage and send a form](#scrape-javascript-rendered-webpage-and-send-a-form)
    - [Scrape Javascript rendered webpage and execute a Javascript code snippet](#scrape-javascript-rendered-webpage-and-execute-a-javascript-code-snippet)
    - [Residential proxy](#residential-proxy)
    - [Session across multiple requests](#session-across-multiple-requests)
  - [HTML parsing](#html-parsing)
  - [Production examples (Amazon, eBay, Zalando, etc)](#production-examples)
  - [Next level: saved requests](#next-level-saved-requests)
  - [Additional info](#additional-info)

### Features

  - Request forging (method, body, headers, referer, useragent, authentication, cookies)
  - Javascript rendered websites scraping (including loading and rendering parameters)
  - Embedded HTML parsing system (returns a JSON object containing the data you need)
  - Geo-located datacenter & residential rotating proxies
  - Sessions (keep the same ip address across multiple requests)
  - Saved requests (create skeleton requests with predefined HTML output schemas and override variables such as the target URL directly from your app. If the target website structure changes, no need to modify your code, just edit the output schema in your dashboard and your app will start scraping again!)

&nbsp;
### Options

```bash
POST - https://api.wintr.com/fetch
```

| Option | Type   | Description | Example | Value |
| ------ | ------ | ----------- | ------- | ----- |
| url `*` | `string` | Target URL | `"https://api.wintr.com/test"` | * (default=null) |
| apikey `*` | `string` | Your API key | `"5d6bf3bd36"` | * (default=null) |
| countrycodes | `array` | An array of country codes to scrape the webpage from (the IP address used to scrape the page will be located in one of the provided countries) | `["uk", "fr"]` | "us","uk","de","nl","fr","es","it","cz","pl" (default=null) |
| proxytype | `string` | The type of proxy you want to use for this request (residential IP addresses have a lower fail rate) | `"residential"` | "local","residential" (default="local") - CONSUMES 15 API CREDITS (instead of 1) |
| session | `string` | Define a session name an reuse it to keep the same IP address across multiple scraping requests | `"my_session"` | * (default=null) - ONLY WORKS IF proxytype="residential" |
| referer | `string` | Request referer | `"https://www.google.com"` | * (default=null) |
| useragent | `string` | Request user agent | `"Mozilla/5.0"` | * (default=null) |
| headers | `object` | Request headers | `{ "X-header1": "value1", "X-header2": "value2" }` | * (default=null) |
| method | `string` | Request method | `"POST"` | "GET","PATCH","POST","PUT" (default="GET") |
| body | `object` | Request input data | `{ "name1": "value1", "name2": "value2" }` | * (default=null) |
| auth | `object` | Request HTTP authentication | `{ "user": "user123", "pass": "password123" }` | * (default=null) |
| jsrender | `bool` | If true, the Javascript code on the target webpage will be executed before returning the HTML data (useful to scrape Single Page Applications made with AngularJS for example) | `true` | true,false (default=false) - CONSUMES 10 API CREDITS (instead of 1) |
| renderuntil | `string` | Stops page rendering after the provided event | `"domloaded"` | "domloaded"|"networkloaded" (default="networkloaded") - ONLY WORKS IF jsrender=true |
| loadall | `bool` | Load all the page assets including ads, analytics, etc... | `true` | true,false (default=false) - ONLY WORKS IF jsrender=true |
| waitfor | `number`/`string` | Wait for the provided amount of time (in seconds) or CSS selector to appear before returning the HTML data | `".navigation"` | * (default=null) - ONLY WORKS IF jsrender=true |
| cookies | `array` | Allows you to inject cookies on the browser instance that will scrape the page for you | `[ { "name": "cookie1", "value": "value1", "domain": ".example.com" } ]` | * (default=null) - ONLY WORKS IF jsrender=true |
| sendform | `object` | Allows you to fill a form and send it in the browser instance | `{ "fields": [ { "selector": "#myfield", "value": "myvalue" } ], "buttons": [ "#mybutton" ], "pressenter": false }` | * (default=null) - ONLY WORKS IF jsrender=true |
| executejs | `string` | Allows you to execute JS code in the browser instance (the code should be Base64 encoded) | `Y29uc29sZS5sb2coJ3Rlc3QnKTs=` | * (default=null) - ONLY WORKS IF jsrender=true |
| outputschema | `object` | A flexible JSON output schema to order the data in the way you wan't to get it | [see examples](#html-parsing) | * (default=null) |

&nbsp;
### Examples

#### Scrape a webpage
[video tutorial](https://www.youtube.com/watch?v=krWF_qY66f4)
```js
const axios = require('axios')
const options = {
    method: 'post',
    responseType: 'json',
    data: {
        url: 'TARGET_URL',
        apikey: 'WINTR_API_KEY'
    },
    url: 'https://api.wintr.com/fetch'
}

axios(options)
.then((result) => {
    console.log(result)
})
.catch((err) => {
     console.error(err)
})
```

#### Geo-located request
[video tutorial](https://www.youtube.com/watch?v=39A8JQOvyJc)
```js
const axios = require('axios')
const options = {
    method: 'post',
    responseType: 'json',
    data: {
        url: 'TARGET_URL',
        apikey: 'WINTR_API_KEY',
        countrycodes: [
            'CUSTOM_COUNTRY_CODE'
        ]
    },
    url: 'https://api.wintr.com/fetch'
}

axios(options)
.then((result) => {
    console.log(result)
})
.catch((err) => {
    console.error(err)
})
```

#### Custom referer
[video tutorial](https://www.youtube.com/watch?v=39A8JQOvyJc)
```js
const axios = require('axios')
const options = {
    method: 'post',
    responseType: 'json',
    data: {
        url: 'TARGET_URL',
        apikey: 'WINTR_API_KEY',
        referer: 'CUSTOM_REFERER'
    },
    url: 'https://api.wintr.com/fetch'
}

axios(options)
.then((result) => {
    console.log(result)
})
.catch((err) => {
    console.error(err)
})
```

#### Custom useragent
[video tutorial](https://www.youtube.com/watch?v=39A8JQOvyJc)
```js
const axios = require('axios')
const options = {
    method: 'post',
    responseType: 'json',
    data: {
        url: 'TARGET_URL',
        apikey: 'WINTR_API_KEY',
        useragent: 'CUSTOM_USERAGENT'
    },
    url: 'https://api.wintr.com/fetch'
}

axios(options)
.then((result) => {
    console.log(result)
})
.catch((err) => {
    console.error(err)
})
```

#### Custom headers
[video tutorial](https://www.youtube.com/watch?v=39A8JQOvyJc)
```js
const axios = require('axios')
const options = {
    method: 'post',
    responseType: 'json',
    data: {
        url: 'TARGET_URL',
        apikey: 'WINTR_API_KEY',
        headers: {
            'X-HEADER1': 'VALUE1',
            'X-HEADER2': 'VALUE2'
        }
    },
    url: 'https://api.wintr.com/fetch'
}

axios(options)
.then((result) => {
    console.log(result)
})
.catch((err) => {
    console.error(err)
})
```

#### Custom method
[video tutorial](https://www.youtube.com/watch?v=39A8JQOvyJc)
```js
const axios = require('axios')
const options = {
    method: 'post',
    responseType: 'json',
    data: {
        url: 'TARGET_URL',
        apikey: 'WINTR_API_KEY',
        method: 'POST',
        body: {
            'NAME1': 'VALUE1',
            'NAME2': 'VALUE2'
        }
    },
    url: 'https://api.wintr.com/fetch'
}

axios(options)
.then((result) => {
    console.log(result)
})
.catch((err) => {
    console.error(err)
})
```

#### HTTP authentication
[video tutorial](https://www.youtube.com/watch?v=39A8JQOvyJc)
```js
const axios = require('axios')
const options = {
    method: 'post',
    responseType: 'json',
    data: {
        url: 'TARGET_URL',
        apikey: 'WINTR_API_KEY',
        auth: {
            'user': 'USERNAME',
            'pass': 'PASSWORD'
        }
    },
    url: 'https://api.wintr.com/fetch'
}

axios(options)
.then((result) => {
    console.log(result)
})
.catch((err) => {
    console.error(err)
})
```

#### Scrape Javascript rendered webpage
[video tutorial](https://www.youtube.com/watch?v=7LpYWWjDmik)
```js
const axios = require('axios')
const options = {
    method: 'post',
    responseType: 'json',
    data: {
        url: 'TARGET_URL',
        apikey: 'WINTR_API_KEY',
        jsrender: true
    },
    url: 'https://api.wintr.com/fetch'
}

axios(options)
.then((result) => {
    console.log(result)
})
.catch((err) => {
    console.error(err)
})
```

#### Scrape Javascript rendered webpage rendering only the DOM
(should fasten the request but some part of the page might not be loaded)
[video tutorial](https://www.youtube.com/watch?v=7LpYWWjDmik)
```js
const axios = require('axios')
const options = {
    method: 'post',
    responseType: 'json',
    data: {
        url: 'TARGET_URL',
        apikey: 'WINTR_API_KEY',
        jsrender: true,
        renderuntil: 'domloaded'
    },
    url: 'https://api.wintr.com/fetch'
}

axios(options)
.then((result) => {
    console.log(result)
})
.catch((err) => {
    console.error(err)
})
```

#### Scrape Javascript rendered webpage including external assets
(load all page assets including ads but might slow down the request)
[video tutorial](https://www.youtube.com/watch?v=7LpYWWjDmik)
```js
const axios = require('axios')
const options = {
    method: 'post',
    responseType: 'json',
    data: {
        url: 'TARGET_URL',
        apikey: 'WINTR_API_KEY',
        jsrender: true,
        loadall: true
    },
    url: 'https://api.wintr.com/fetch'
}

axios(options)
.then((result) => {
    console.log(result)
})
.catch((err) => {
    console.error(err)
})
```

#### Scrape Javascript rendered webpage & wait 5 seconds
(30 seconds max and will not auto-retry in case of failure)
[video tutorial](https://www.youtube.com/watch?v=7LpYWWjDmik)
```js
const axios = require('axios')
const options = {
    method: 'post',
    responseType: 'json',
    data: {
        url: 'TARGET_URL',
        apikey: 'WINTR_API_KEY',
        jsrender: true,
        waitfor: 5
    },
    url: 'https://api.wintr.com/fetch'
}

axios(options)
.then((result) => {
    console.log(result)
})
.catch((err) => {
    console.error(err)
})
```

#### Scrape Javascript rendered webpage & wait for element to load
(30 seconds timeout and will not auto-retry in case of failure)
[video tutorial](https://www.youtube.com/watch?v=7LpYWWjDmik)
```js
const axios = require('axios')
const options = {
    method: 'post',
    responseType: 'json',
    data: {
        url: 'TARGET_URL',
        apikey: 'WINTR_API_KEY',
        jsrender: true,
        waitfor: 'CSS_SELECTOR'
    },
    url: 'https://api.wintr.com/fetch'
}

axios(options)
.then((result) => {
    console.log(result)
})
.catch((err) => {
    console.error(err)
})
```

#### Scrape Javascript rendered webpage with custom cookies
(possible values: `name`, `value`, `domain`, `path`, `expires`, `size`, `httpOnly`, `secure`, `session`, `sameSite`)
[video tutorial](https://www.youtube.com/watch?v=7LpYWWjDmik)
```js
const axios = require('axios')
const options = {
    method: 'post',
    responseType: 'json',
    data: {
        url: 'TARGET_URL',
        apikey: 'WINTR_API_KEY',
        jsrender: true,
        cookies: [{
            'name': 'cookie1',
            'value': 'value1',
            'domain': '.example.com'
        }]
    },
    url: 'https://api.wintr.com/fetch'
}

axios(options)
.then((result) => {
    console.log(result)
})
.catch((err) => {
    console.error(err)
})
```

#### Scrape Javascript rendered webpage and send a form
To take advantage of this feature, combine it with the [waitfor](#scrape-javascript-rendered-webpage--wait-for-element-to-load) parameter to wait for a CSS selector that is only rendered after the form has been sent!
If `pressenter` is `true`, the browser will press the `Enter` key once the input fields are filled.
(sendform has a 15 seconds timeout and will not auto-retry in case of failure)
[video tutorial](https://www.youtube.com/watch?v=7LpYWWjDmik)
```js
const axios = require('axios')
const options = {
    method: 'post',
    responseType: 'json',
    data: {
        url: 'TARGET_URL',
        apikey: 'WINTR_API_KEY',
        jsrender: true,
        sendform: { 
            "fields": [
                { 
                    "selector": "#myfield", 
                    "value": "myvalue" 
                } 
            ], 
            "buttons": [ 
                "#mybutton" 
            ], 
            "pressenter": false 
        }
    },
    url: 'https://api.wintr.com/fetch'
}

axios(options)
.then((result) => {
    console.log(result)
})
.catch((err) => {
    console.error(err)
})
```

#### Scrape Javascript rendered webpage and execute a Javascript code snippet
[video tutorial](https://www.youtube.com/watch?v=7LpYWWjDmik)
```js
const axios = require('axios')
const options = {
    method: 'post',
    responseType: 'json',
    data: {
        url: 'TARGET_URL',
        apikey: 'WINTR_API_KEY',
        jsrender: true,
        executejs: Buffer.from('window.scrollTo(0, document.body.scrollHeight);').toString('base64')
    },
    url: 'https://api.wintr.com/fetch'
}

axios(options)
.then((result) => {
    console.log(result)
})
.catch((err) => {
    console.error(err)
})
```

#### Residential proxy
[video tutorial](https://www.youtube.com/watch?v=UENq57dsJjg)
```js
const axios = require('axios')
const options = {
    method: 'post',
    responseType: 'json',
    data: {
        url: 'TARGET_URL',
        apikey: 'WINTR_API_KEY',
        proxytype: 'residential'
    },
    url: 'https://api.wintr.com/fetch'
}

axios(options)
.then((result) => {
    console.log(result)
})
.catch((err) => {
    console.error(err)
})
```

#### Session across multiple requests
(sessions lasts for **120 seconds**, if you want to define the geo-location of your session, you have to set the `countrycodes (array)` parameter with one and only country code at the creation of the session, if you created the session before setting the `countrycodes (array)` parameter, you need to create a session with a **different name** to proceed or **wait the current one to expire** after 120 seconds.)

[video tutorial](https://www.youtube.com/watch?v=UENq57dsJjg)

```js
const axios = require('axios')
const options = {
    method: 'post',
    responseType: 'json',
    data: {
        url: 'TARGET_URL',
        apikey: 'WINTR_API_KEY',
        proxytype: 'residential',
        session: 'MY_SESSION_NAME'
    },
    url: 'https://api.wintr.com/fetch'
}

axios(options)
.then((result) => {
    console.log(result)
})
.catch((err) => {
    console.error(err)
})
```

&nbsp;
### HTML parsing
  - if you want to parse the HTML content the `outputschema (object)` should respect certain conditions:
    - the `outputschema (object)` child object(s) **can only contain those keys**:
      - `group (string)`: the main **CSS selector**
      - `data (object)`: the **data structure**
    - the `data (object)` child object(s) **must contain those keys**:
      - `selector (string)`: **CSS selector** of the element itself or one of it\'s childs
      - `attr (string)`: the HTML attribute or **"*html*"** or **"*text*"**
      - and optionally those keys:
          - `modifier (array)`: a list of **modification to apply** to the data
          - `replacer (array)`: a **search and replace** based array objects
    - if present, the `modifier (array)` **accepted values** are those (strings):
      - `"touppercase"`: turn data to uppercase strings
      - `"tolowercase"`: turn data to lowercase strings
      - `"tonumber"`: turn data to primitive numbers
      - `"totrimmed"`: turn data to trimmed strings
    - if present, the `replacer (array)` **accepted values are** `(objects)` **which must contain 2** `(strings)`:
      - `search (string)`: the string to find
      - `replace (string)`: the replacing string
  - The parsing process **is made in the order** you set the `modifier (array)` and `replacer (array)` elements in your `outputschema (object)`
  - each modification or replacement will be done **in the order of the provided** `(arrays)`

[video tutorial](https://www.youtube.com/watch?v=dcZODhL2_ek)

**this example is getting all the page link anchors, replacing the linebreaks `\n` on them, trimming them and turning them to uppercase**

```js
const axios = require('axios')
const options = {
    method: 'post',
    responseType: 'json',
    data: {
        url: 'TARGET_URL',
        apikey: 'WINTR_API_KEY',
        outputschema: {
            links: {
                group: 'a',
                data: {
                    link_anchor_without_linebreak_and_trimmed_and_to_uppercase: {
                        selector: 'a',
                        attr: '*text*',
                        replacer: [{
                            search: '\n',
                            replace: ''
                        }],
                        modifier: [
                            'totrimmed',
                            'touppercase'
                        ]
                    },
                    link_target: {
                        selector: 'a',
                        attr: 'href'
                    }
                }
            }
        }
    },
    url: 'https://api.wintr.com/fetch'
}

axios(options)
.then((result) => {
    console.log(result)
})
.catch((err) => {
    console.error(err)
})
```

&nbsp;
### Production examples 

  - Check out our curated list of example to scrape from **Amazon**, **Ebay**, **Zalando**, and many more in our [examples section](https://github.com/wintrdotcom/wintrAPI/tree/master/examples)
  - If you are already familiar with WINTR saved requests check our request files to scrape from **Amazon**, **Ebay**, **Zalando** and many more in the [saved requests section](https://github.com/wintrdotcom/wintrAPI/tree/master/saved_requests)

&nbsp;
### Next level: saved requests!
Imagine that you are **scraping Amazon products** for your website and that you plan to **get income from affilation**, you will need to watch out for Amazon\'s product page **HTML structure changes**. the best practice is to combine **HTML parsing** and **saved request** features for a 99.99% uptime scraping system.
  - **sign-in** to your WINTR dashboard querybuilder **[in here](https://www.wintr.com/dashboard-querybuilder)**
  - **create the Amazon scraping request** on a product list page such as **[this one](https://www.amazon.com/s?k=iphone)** with an `outputschema` parameter that will retrieve the **name, thumbnail, link and price** of the products. (**[see an Amazon request example here](https://gist.github.com/wintrdotcom/e068a356fb725f9fd04baab5b5cd59e2)**)
  - click the **save** button, it will generate a saved request that you can call like that: **GET - https://api.wintr.com/savedrequest/SAVED_REQUEST_NAME?apikey=WINTR_API_KEY**
  - your application can now scrape the products **name, thumbnail, link and price** of the **targetted Amazon page** like that:

```js
const axios = require('axios')

axios('https://api.wintr.com/savedrequest/SAVED_REQUEST_NAME?apikey=WINTR_API_KEY')
.then((result) => {
    console.log(result)
})
.catch((err) => {
    console.error(err)
})
```
  - your application can **override the saved request target url** and other parameters to scrape other Amazon products **name, thumbnail, link and price** like that:
  
```js
const axios = require('axios')
axios('https://api.wintr.com/savedrequest/SAVED_REQUEST_NAME?apikey=WINTR_API_KEY&url=' + encodeURIComponent('https://www.amazon.com/s?k=SOME_OTHER_KEYWORDS'))
.then((result) => {
    console.log(result)
})
.catch((err) => {
    console.error(err)
})
```
  - following these steps, all you have to do is to **setup an alert** if the data returned by your saved request misses the **name, thumbnail, link or price** and modify the output schema of your saved request in the WINTR dashboard if Amazon changed its result page structure. this allows you to **NEVER EDIT YOUR PROGRAM AGAIN AND ENJOY 99.99% UPTIME!**

[video tutorial](https://www.youtube.com/watch?v=FABvKdt-_28)
 
&nbsp;
### Additional info

  - the maximum scrapable document size is set to **10mb**
  - the timeout is set to **60 seconds** for each try
  - if a request is failing for an unknown reason it will automatically retry, **3 tries maximum** don't forget to set your timeout to atleast **120 seconds** to ensure everything goes smoothly
  - please ensure that you are respecting the conccurent requests rate of your subscribed plan to avoid 429 - **ERR_TOO_MANY_REQUESTS**
  - on each try, the proxy ip address will be different and if you did not set them, **some headers might be automatically set**:
    - `User-Agent`
    - `Accept`
    - `Accept-Encoding`
    - `Upgrade-Insecure-Requests`
    - `Sec-Fetch-User`
    - `Sec-Fetch-Mode`
    - `Accept-Language`
    - `Referer`
  - the prossible **status codes & errors** are:
    - `200` - **OK** => Request completed successfully
    - `400` - **BAD_REQUEST** => Incorrect parameter(s)
    - `402` - **PAYMENT_REQUIRED** => Invalid API key OR plan quota exceeded
    - `444` - **ERR_NO_RESPONSE** => Target URL responded with XXX error code
    - `429` - **ERR_TOO_MANY_REQUESTS** => You are doing too many requests at once
    - `415` - **ERR_UNSUPPORTED_MEDIA_TYPE** => Binary files are not supported
    - `403` - **ERR_TOO_BIG** => Response size is bigger than 10MB
    - `408` - **ERR_TIMEOUT** => Timeout of 60 seconds exceeded
    - `404` - **ERR_NOT_FOUND** => Target URL unreachable
    - `5XX` - **UNKNOWN_ERROR** => You won't be charged for this request
