jwt-csrf
========
[![Dependency Status](http://daviddm-5042.ccg21.dev.paypalcorp.com:1337/NodeXOShared/jwt-csrf.svg)](http://daviddm-5042.ccg21.dev.paypalcorp.com:1337/NodeXOShared/jwt-csrf)
[![devDependency Status](http://daviddm-5042.ccg21.dev.paypalcorp.com:1337/NodeXOShared/jwt-csrf/dev-status.svg)](http://daviddm-5042.ccg21.dev.paypalcorp.com:1337/NodeXOShared/jwt-csrf#info=devDependencies)

Jwt middleware provider for hermes

Can be used as simple API or a middleware. Internally uses `jsonwebtoken` ([Here](https://github.com/auth0/node-jsonwebtoken))
and `crypto-paypal` modules.

## As API:

### Create JWT:

Usage:

```
 var token = create(options, req);

```

`options` is json which must contain `secret` and optional `expiresInMinutes`

  1. Construct the token.

       Format (for logged in cases): userAgent:payerId

       Format (for non logged in cases): userAgent

  2. Encrypt the token using `ppcryptutils` module, with `options.secret` and `options.macKey`

  3. Take encrypted value from step #2 and use `jsonwebtoken.sign`

  4. return result from from step #3.


### Validate JWT:

Will return `true` if the token validation succeeds, else returns false.

```
 var validate = validate(options, req, callback);

```

`options` is json which must contain `secret` and `macKey`.


Checks for JWT in `req.headers['X-CSRF-JWT']`

 1. If the request is POST, then get the jwt from headers['X-CSRF-JWT]. If token is not present then send a 401   response.
 
 2. Try decoding JWT. JWT decoding logic will throw error if the payload does not match the encrypted value.
 JWT is a self verifying token. If decoding throws error then send a 401.

 3. If this is a logged in user, decrypt the payload. For logged in case decrypted payload will be of the form
 user-agent:payerId.

 Verify payerId from above decrypted value with the user's encrypted payerid.

 4. Additionally for both logged in and not authenticated user, match user agent as a additional level of security.

`callback` will be called with err if there is any error in decryption. Or else it will be called with
callback(null, result). result could be true or false depending on whether validation succeeds or fails.

## As Middlware:

 Usage

 ```
   var middleware = jwt-csrf.middleware(options);

 ```

`options` is json which must contain `secret`, `macKey` and an optional `expiresInMinutes`


Returns a middlware function which takes `req`, `res`, `next`.

The middleware sets the JWT in req headers under `'X-CSRF-JWT'`.

It also validates the `'X-CSRF-JWT'` in incoming request. If validation succeeds calls `next`. Else, does `res.status
(401)`
calls
`next` with a error.



## Testing
`$ npm test`

`$ npm run cover`
