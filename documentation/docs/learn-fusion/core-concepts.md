---
title: Core concepts
path: /core-concepts/
---

# Core Concepts

### Middleware

A middleware in Fusion.js is similar to middleware in HTTP servers like Express.js (in fact, they are built on top of Koa middleware). Fusion.js middleware are functions that run on the server when an HTTP request occurs. However, unlike Koa/Express middleware, Fusion.js middleware functions also run on the browser on page load.

### Services

A service is any library that exposes a programmatic API. The main difference between a Fusion.js service and a regular library is that services are dynamically injected via a dependency injection system, whereas libraries would typically be imported statically via ES6 import statements.

### Plugin

A [Fusion.js plugin](/docs/references/creating-a-plugin) is a container that encapsulates a service and a middleware. Plugins declare what dependencies they rely on (via the DI system) and may expose a service, a middleware, or both. Services and middleware that are exposed by a plugin can consume services from other plugins.

Fusion.js plugins are suitable for encapsulating integrations with libraries, even when they require making changes to multiple areas of concern (e.g. server, browser, React providers).

### Dependency injection

Fusion.js has a [type-aware dependency injection system](/docs/references/creating-a-plugin/dependencies). Dependency injection is a way to modularize code, similar to how you would use ES6 import statements instead of putting your whole app in a single file. The main difference is that ES6 import statements are static (meaning they always import the same thing) and dependency injection is dynamic (meaning that what is imported can be changed through runtime configuration, for example for mocking in tests).

---

# Glossary

Fusion.js documentation uses industry jargon that may be specific to web development (and specific technologies employed by Fusion.js) and therefore potentially not familiar to developers who are not primarily web developers. This section provides definitions for such terms:

### Asset

A file that is meant to be served by a web server. Examples of assets include image files, PDF files, CSS files or Javascript files. An asset file may be generated by compilers (e.g. SASS) or other tools such as image compressors, and they may be served via a CDN in a production environment.

An asset is said to be static if it's served from disk without being modified.

### Brotli

A compression algorithm similar to gzip. Typically, it has better compression rates than gzip, but it's not supported by all browsers. Fusion.js has brotli support out of the box for the browsers that do support it.

### Bundle

A bundle is a Javascript file that is compiled from one or more source files and served to a browser. By default, Fusion.js compiles two bundles: a main bundle (with application logic) and a vendor bundle (with libraries). It may additionally output more bundles if an application employs code splitting.

### CDN

CDN stands for Content Delivery Network. It's a collection of servers around the world that are used to serve [assets](#asset) from closer physical proximity to the clients requesting them, in order to reduce network latency.

### Chunk

Typically chunks are an in-memory representation of a [bundle](#bundle). The term is often used when describing Webpack internals, or to refer to chunk ids (which can be thought of as bundle identifiers).

### Code fence

A conditional statement whose body only runs server-only code or browser-only code (i.e. `if (__NODE__) {}` and `if (__BROWSER__) {}`)

### Code splitting

Code splitting (aka bundle splitting) is a technique that lazily loads parts of an application to reduce the initial download time of an application. Fusion.js supports code splitting via `fusion-react`.

### Context

A [context](/api/fusion-core#context) is a state object that represents the HTTP request and response values in middleware on the server. The context object can also hold intermediate state such as a React tree. Each HTTP request has exactly one context object associated with it, which is passed from middleware to middleware. Once all middleware in an application have finished running for a request in the server, this state object is translated into an HTTP response. The context object can also be used by middleware on the browser.

### CSRF

CSRF stands for Cross-Site Request Forgery. It's a type of security attack that tricks a user to visit a malicious site while authenticated to a service and spoofs requests to the server by exploiting the user's credentials. Fusion.js provides protection against this type of attack via `fusion-plugin-csrf-protection`.

### Dependency graph resolution

A mechanism that sorts dependencies and runs them in order, so that plugins that depend on other plugins always run after the ones they depend on.

### DI

DI stands for [dependency injection](#dependency-injection) system.

### Downstream / Upstream

Fusion.js middleware functions must call and await a `next` function. Everything that happens before the `next` function call is known as the "downstream". Everything that happens after it is the "upstream". The `next` function call itself represents React rendering.

If an application has many plugins (or rather, many middleware), all the downstreams are run in order of dependency. Then, rendering occurs, then all the upstreams are run in reverse order.

### Dynamic importing

Refers to the ability to use `import('some/dependency')` calls to lazily import a module. This syntax is not technically part of the Javascript standard (yet), but supported by Webpack and Fusion.js.

### Enzyme

Enzyme is a utility library to help test React components. Fusion.js provides integrated support for Enzyme.

### Evergreen browser

A modern browser that supports the majority of HTML5 and ES6 features.

### HMR

HMR stands for Hot Module Replacement. It's a development productivity tool that automatically recompiles and applies code changes to a running application without restarting the server or refreshing the browser. It comes enabled out of the box with Fusion.js.

### I18N

I18N stands for internationalization. In the context of Fusion.js, it typically refers to tools for working with translations.

### LTS

LTS stands for Long Term Support. The term is used to refer to the latest stable versions of hard dependencies such as Node.js.

### Jest

Jest is a testing framework with support for snapshot testing. Fusion.js provides integrated support for Jest.

### Lint

A linter is a tool that statically analyzes code and reports certain patterns of code if they are error-prone or if they deviate from a code style standard.

### Prettier

An opinionated code formatter that ensures code consistency. Similar to Go's gofmt.

### Redux

Redux is a popular state management library that is commonly used with React.

### REST

REST stands for Representational State Transfer. In the context of web development, it refers specifically to HTTP endpoint architectures that leverage HTTP method names and object-oriented endpoint naming (in contrast to RPC, which revolves around verb-oriented endpoint naming).

### RPC

RPC stands for Remote Procedure Call. In the context of web development, it refers specifically to HTTP endpoint architectures revolving around named method definitions (in contrast to REST architectures, which revolve around leveraging HTTP method names and noun-oriented endpoint naming).

### SPA

SPA stands for Single Page Application. In the context of React, it means an application that uses react-router and the HTML5 history.pushState API under the hood for page transitions, as opposed to traditional JSP-style applications where page transitions are accomplished via full page refreshes.

### SSR

SSR stands for Server-Side Rendering. It means rendering a React tree on the server and serving the output as HTML (while also using the same Javascript code in the browser in a single-page application architecture). This can improve time-to-first-paint because browsers' HTML rendering engine is faster than Javascript/DOM-based rendering. Server-side rendering is enabled by default in Fusion.js.

### Token

A token is an identifier for a dependency in Fusion.js' DI system. Typically, you declare that a plugin depends on a token and bind it to a local variable name (e.g. the declaration `{deps: {logger: LoggerToken}}` means to make available a local variable called `logger` whose value is whatever service we specified `LoggerToken` to point to). `LoggerToken` could in turn point to a hypothetical WinstonService or a ConsoleService depending on how you want to configure your application.

### Tree-shaking

Tree-shaking is a compiler optimization that removes unused imports. In Fusion.js, tree-shaking is used to remove server-side code from universal code when generating browser bundles. Note that tree-shaking may not occur if an unused dependency has side effects upon being imported.

### Universal code

Universal code refers to code that lives in a single file but runs on both the server and the browser. Note that it's possible to conditionally run code only on the server or only on the browser via `if (__NODE__) {}` and `if (__BROWSER__) {}` statements, respectively.

### Universal rendering

[Universal rendering](/docs/references/universal-rendering) refers to code that lives in a single file but renders HTML on the server and hydrates in the browser.

### Yarn

Yarn is a package manager for Node.js, similar to (and interchangeable with) NPM. When using Fusion.js, Yarn is recommended over NPM because it generally provides faster and more reliable installs.

### Zopfli

A compression algorithm, compatible with gzip. Provides better compression rates than regular gzip. Fusion.js uses zopfli compression by default.
