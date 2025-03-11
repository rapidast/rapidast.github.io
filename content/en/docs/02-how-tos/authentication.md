---
title: Authentication
description: 
weight: 2
---

Authentication is configured in the `general` entry, as it can be applied to multiple scanning options. Currently, Authentication is applied to [ZAP] scanning only. In the long term it may be applied to other scanning configurations.

## Supported options:

### No authentication:
The scanners will communicate anonymously with the application

###  OAuth2 using a Refresh Token:
This method describes required parameters needed to retrieve an access token, using a refresh token as a secret.

  - authentication type : `oauth2_rtoken`
  - parameters :
    - `token_endpoint`: the URL to which send the refresh token
    - `client_id` : the client ID
    - `rtoken_from_var`: for practical reasons, the refresh token is provided using environment variables. This entry describes the name of the variable containing the secret refresh token
    - `preauth`: Pre-generate a token and force ZAP to use it throughout the session (the session token will not be refreshed after it's expired). Default: False. This is only useful for scans sufficiently short that it will be finished before the token expires

###  HTTP Basic:
This method describes the HTTP Basic Authorization Header. The username and password must be provided in plaintext and will be encoded by the scanners

  - authentication type: `http_basic`
  - parameters:
    - `username`
    - `password`

###  HTTP Header:
This method describes the HTTP generic header. The name and value must be provided in plaintext.
  - authentication type: `http_header`
  - parameters:
    - `name`: the header name added to every request. By default is `Authorization`
    - `value` or `value_from_var` (the environment variable with the secret)

###  Cookie Authentication
This method describes authentication via Cookie header. The cookie name and value must be provided in plaintext.

  - authentication type: `cookie`
  - parameters:
    - `name`
    - `value`

###  Browser authentication
This method uses firefox in the background to load a login page and fill in username/password, and will retrieve and set the session cookies accordingly.
  - authentication type: `browser`
  - parameters:
    - `username`
    - `password`
    - `loginPageUrl`: the URL to the login page (either the full URL, or relative to the `application.url` value)
    - `loginPageWait`: The number of seconds to wait after submitting the login form before the browser is closed. (default: 2)
    - `verifyUrl`: a URL that "proves" the user is authenticated (either the full URL, or relative to the `application.url` value). This URL must return a success if the user is correctly authenticated, and an error otherwise.
    - `loggedInRegex`: Regex pattern used to identify Logged in messages (default: `\\Q 200 OK\\`)
    - `loggedOutRegex`: Regex pattern used to identify Logged Out messages (default: `\\Q 403 Forbidden\\`)