# 🕷️ Xcrap Pupeeteer Client

**Xcrap Puppeteer Client** is a package of the Xcrap framework that implements an HTTP client using the [Puppeteer](https://www.npmjs.com/package/puppeteer) library.

## 📦 Installation

There are no secrets to installing it, just use your favorite dependency manager. Here is an example using NPM:

```cmd
npm i @xcrap/puppeteer-client @xcrap/core @xcrap/extractor
```

> You need to install `@xcrap/extractor` and `@xcrap/core` as well because I left them as `peerDependencies`, which means that the package needs `@xcrap/extractor` and `@xcrap/core` as dependencies, however, the ones that the user has installed in the project will be used.

## 🚀 Usage

Like any HTTP client, `PuppteerClient` has two methods: `fetch()` to make a request for a specific URL and `fetchMany()` to make requests for multiple URLs at the same time, being able to control concurrency and delays between requests.

### Example usage

```ts
import { PuppteerClient } from "@xcrap/puppeteer-client"
import { extract } from "@xcrap/extractor"

;(async() => {
    const client = new PuppteerClient()
    const url = "https://example.com"
    const response = await client.fetch({ url: url })
    const parser = response.asHtmlParser()
    const pageTitle = await parser.extractValue({ query: "title", extractor: extract("innerText") })

    console.log("Page Title:", pageTitle)
})();
```

### Using Actions

If you want to perform operations on the page before or after requests, you can use the `actions` property, which is an array of functions. Actions are flexible enough that you can do exactly what you would normally do with Puppeteer: login, click buttons, evaluate functions, etc.

```ts
const response = await client.fetch({
    url: "https://example.com",
    actions: [
        async (page) => {
            await page.type("#username", "user")
            await page.type("#password", "mypassword123")
            await page.click("#submit")
        }
    ]
})
```

By default, an action is executed after requests. If you want to manually define when it will be executed, you will have to pass an object instead of a simple function:

```ts
const response = await client.fetch({
    url: "https://example.com",
    actions: [
        {
            type: "afterRequest", // Executed after the request
            exec: async (page) => {
                await page.type("#username", "user")
                await page.type("#password", "mypassword123")
                await page.click("#submit")
            }
        },
        {
            type: "beforeRequest", // Executed before the request
            func: async (page) => {
                const width = 1920 + Math.floor(Math.random() * 100)
                const height = 3000 + Math.floor(Math.random() * 100)

                await page.setViewport({
                    width: width,
                    height: height,
                    deviceScaleFactor: 1,
                    hasTouch: false,
                    isLandscape: false,
                    isMobile: false,
                })
            }
        }
    ]
})
```

### Adding a proxy

In an HTTP client that extends `BaseClient` we can add a proxy in the constructor as we can see in the following example:

1. **Providing a `proxy` string:

```ts
const client = new PuppteerClient({ proxy: "http://47.251.122.81:8888" })
```

2. **Providing a function that will generate a `proxy`:**

```ts
function randomProxy() {
    const proxies = [
        "http://47.251.122.81:8888",
        "http://159.203.61.169:3128"
    ]

    const randomIndex = Math.floor(Math.random() * proxies.length)

    return proxies[randomIndex]
}

const client = new PuppteerClient({ proxy: randomProxy })
```

### Using a custom User Agent

In a client that extends `BaseClient` we can also customize the `User-Agent` of the requests. We can do this in two ways:

1. **By providing a `userAgent` string:

```ts
const client = new PuppteerClient({ userAgent: "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/134.0.0.0 Safari/537.36" })
```

2. **By providing a function that will generate a `userAgent`:**

```ts
function randomUserAgent() {
    const userAgents = [
        "Mozilla/5.0 (iPhone; CPU iPhone OS 9_8_4; like Mac OS X) AppleWebKit/603.37 (KHTML, like Gecko) Chrome/54.0.1244.188 Mobile Safari/601.5", "Mozilla/5.0 (Windows NT 10.3;; en-US) AppleWebKit/537.35 (KHTML, like Gecko) Chrome/47.0.1707.185 Safari/601"
    ]

    const randomIndex = Math.floor(Math.random() * userAgents.length)

    return userAgents[randomIndex]
}

const client = new PuppteerClient({ userAgent: randomUserAgent })
```

### Using custom Proxy URL

In a client that extends `BaseClient` we can use proxy URLs, I don't know how to explain to you how they work, but I kind of discovered this kind of porxy when I was trying to solve the CORS problem by making a request on the client side, and then I met the *CORS Proxy*. Here I have a [template](https://gist.github.com/marcuth/9fbd321b011da44d1287faae31a8dd3a) for one for CloudFlare Workers in case you want to roll your own.

Well, we can do it the same way we did with `userAgent`:

1. **Providing a `proxyUrl` string:

```ts
const client = new PuppteerClient({ proxyUrl: "https://my-proxy-app.my-username.workers.dev" })
```

2. **Providing a function that will generate a `proxyUrl`:**

```ts
function randomProxyUrl() {
    const proxyUrls = [
        "https://my-proxy-app.my-username-1.workers.dev",
        "https://my-proxy-app.my-username-2.workers.dev"
    ]

    const randomIndex = Math.floor(Math.random() * proxyUrls.length)

    return proxyUrls[randomIndex]
}

const client = new PuppteerClient({ proxyUrl: randomProxyUrl })
```

## 🤝 Contributing

- Want to contribute? Follow these steps:
- Fork the repository.
- Create a new branch (git checkout -b feature-new).
- Commit your changes (git commit -m 'Add new feature').
- Push to the branch (git push origin feature-new).
- Open a Pull Request.

## 📝 License

This project is licensed under the MIT License.