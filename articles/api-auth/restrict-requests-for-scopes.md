---
  description: Writing rules to restrict user/client access to an API
---

# Restrict Client or User Requests for API Scopes

By default, any user associated with an [Auth0 client](/clients) can request an API's scope(s). If you would like to restrict access to the API's scopes based on the user's role, client association, location, and so on, you can do so via [rules](/rules). Then, if a restricted user attempts to request scopes not permitted to them, they will receive an `HTTP 401` response.

## Example: Deny access based on the API audience

The following [rule](/rules), demonstrates how you would deny access on an API, depending on the `audience` parameter. In this example, we deny access to all users, if the API they are trying to access has the `audience` set to `http://todoapi2.api`.

```js
function (user, context, callback) {

  /*
   *  Denies access to user-based flows based on audience
   */

  var audience = '';

  audience = audience
              || (context.request && context.request.query && context.request.query.audience)
              || (context.request && context.request.body && context.request.body.audience);

  if (audience === 'http://todoapi2.api' || !audience) {
    return callback(new UnauthorizedError('end_users_not_allowed'));
  }

  return callback(null, user, context);
}
```

::: note
The value of an API's `audience` is displayed at the **API Audience** field, at [Dashboard > APIs](${manage_url}/#/apis).
:::

## Example: Deny access based on the Client ID

The following [rule](/rules), demonstrates how you would deny access on an API, depending on the client the user is associated with. In this example, we deny access to all users, if the client through which they login, has an ID equal to `CLIENT_ID` (this is equivalent to disabling **all** Connections for the client).

```js
function (user, context, callback) {

  /*
   *  Denies access to user-based flows based on client ID
   */

  var client_id = '';
  client_id = context.clientID;

  if (client_id === 'CLIENT_ID') {
    return callback(new UnauthorizedError('end_users_not_allowed'));
  }

  return callback(null, user, context);
}
```

::: note
The value of a client's Id is displayed at the **Client ID** field, at [Dashboard > Clients](${manage_url}/#/clients).
:::
