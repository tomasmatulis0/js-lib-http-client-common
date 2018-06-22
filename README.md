# @paysera/http-client-common

[Axios](https://github.com/axios/axios) requests wrapper.   
Basically we use this library in our [generated JS clients from RAML](https://github.com/paysera/util-raml-code-generator). 

## Installation
```shell
npm i @paysera/http-client-common
```
Include files:
```html
<script type="text/javascript" src="dist/babel.polyfill.js"></script>
<script type="text/javascript" src="dist/lib.js"></script>
```

#### Dependencies
* [`axios`](https://github.com/mzabriskie/axios)
* [`store.js`](https://github.com/marcuswestin/store.js/)

## Usage

#### Without any middleware
```js
import { clientFactory, requestFactory } from '@paysera/http-client-common';

const client = clientFactory(
    'https://demo.com', // base url
);

client.performRequest(requestFactory('get', '/list'));
```

#### With JWT authentication middleware(with session storage token provider)
In this example `client.performRequest` makes request with `Authorization: 'Bearer {accessToken}'` header
```js
import { 
    clientFactory, 
    requestFactory,
    JWTAuthenticationMiddleware,
    SessionStorageTokenProvider,
    Scope,
} from '@paysera/http-client-common';

const client = clientFactory(
    'https://demo.com', // base url
    [
        new JWTAuthenticationMiddleware(
            new Scope('scope:a'),
            new SessionStorageTokenProvider(
                (scope) => ({ scope, accessToken: 'created-token' }), 
                (scope) => ({ scope, accessToken: 'refreshed-token' }),
                'unique',
                'namespace',   
            ),
        ),
    ],
);

client.performRequest(requestFactory('get', '/list'));
```

## Write your own middleware
```js
class MyCustomMiddleware {
    async onRequest(config) {
        // here you can make some changes to config object
        return config;
    }
        
    async onResponseError(error) {
        // here you can make some changes to error response
        return error;
    }
}
```

Under the hood middleware use `axios` interceptors, so basically middleware is wrapper for `axios` interceptors. More about `axios` interceptors you can read [here](https://github.com/axios/axios#interceptors) 
* `onRequest(config)` - will be called every time before making request.
* `onResponseError(error)` - will be called every time after request returns error

## Demo run
```shell
npm run dev
```

## Build
```shell
npm run build
```
