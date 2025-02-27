# Creating endpoints

> Fusion.js provides an [RPC plugin](https://github.com/fusionjs/fusion-plugin-rpc-redux-react) that integrates with Redux and React. We recommend using that RPC plugin rather than writing HTTP endpoints manually.

HTTP endpoints can be created by writing a Fusion.js plugin with an exposed [middleware](/docs/references/creating-a-plugin#middlewares):

To write a middleware plugin, we export a _factory_ function that returns a _middleware_ function. The middleware receives a `ctx` argument that has various properties, and a `next` function that it must call. It works the same way as a [Koa](http://koajs.com) middleware.

Let's say we want to implement a `GET /api/ping` endpoint. This endpoint simply responds with `{ok: 1}`. To do that, we check that the `method` and `path` are correct and we set `body` to the data we want to return:

```js
// src/plugins/example.js
import {createPlugin} from 'fusion-core';

export default createPlugin({
  middleware() {
    return (ctx, next) => {
      if (ctx.method === 'GET' && ctx.path === '/api/ping') {
        ctx.body = {ok: 1};
      }
      return next();
    };
  },
});
```

There's one issue left with the code above: Fusion.js code runs universally by default, but we only want to run that code in the server. To do so, add a [code fence](/docs/references/universal-rendering):

```js
// src/plugins/example.js
import {createPlugin} from 'fusion-core';

export default __NODE__ &&
  createPlugin({
    middleware() {
      return (ctx, next) => {
        if (ctx.method === 'GET' && ctx.path === '/api/ping') {
          ctx.body = {ok: 1};
        }
        return next();
      };
    },
  });
```

Next, register the plugin:

```js
// src/main.js
import App from 'fusion-react';
import Example from './plugins/example';

export default () => {
  const app = new App();
  app.register(Example);
  return app;
};
```

That's it! Now, you can test your new endpoint. From your browser developer console:

```js
fetch('/api/ping')
  .then(r => r.json())
  .then(console.log); // {ok: 1}
```

### Body parsing

Let's implement `POST /api/echo`, which responds with the body of a request. To do that, we can use the `koa-bodyparser` package:

```js
import bodyParser from 'koa-bodyparser';

export default __NODE__ &&
  createPlugin({
    middleware() {
      const parseBody = bodyParser();
      return async (ctx, next) => {
        if (ctx.method === 'POST' && ctx.path === '/api/echo') {
          await parseBody(ctx, () => Promise.resolve());
          ctx.body = ctx.request.body;
        }
        return next();
      };
    },
  });
```

Notice that we `await parseBody()` because body parsing is asynchronous. You can similarly await other asynchronous calls such as database calls or microservice requests.
