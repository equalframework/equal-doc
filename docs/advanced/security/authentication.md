# Authentication

## Authentication manager
The `AuthenticationManager` is dedicated to everything related to user authentication. `eQual` is stateless, meaning that all HTTP requests are accompanied by a header that allows identifying the user who made the request.


## Access token

eQual uses JWT tokens that are exchanged between the back-end and the client (browser ) as HttpOnly cookie.

!!! Note "using CLI"
    There is no authentication using CLI: in command-line context, user is identified as root with full privileges.

During authentication (via the signin controller) a token is generated, according to the AUTH_ACCESS_TOKEN_VALIDITY parameter, and stored by the browser.

The duration defined in AUTH_ACCESS_TOKEN_VALIDITY corresponds to the maximum duration of inactivity of a user session.

When the token's validity limit has been exceeded, the token is deleted by the browser and the user must identify himself again.

!!! Note "Extension of validity"
    Each time a valid session token is used, it authenticates the user for a minimum of 1 hour. The validity of the token is extended if necessary.

