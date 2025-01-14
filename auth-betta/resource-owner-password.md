# Resource Owner Grant

#### Description

The Password grant type is used by first-party clients to exchange a user's credentials for an access token. Since this involves the client asking the user for their password, it should not be used by third party clients. In this flow, the user's username and password are exchanged directly for an access token. The application acts on behalf of the user.

![Basic scheme](../.gitbook/assets/untitled-diagram-page-2.svg)

### Configure Client

Fist step is configure Client for Resource Owner Grant with secret and password grant type:

{% code-tabs %}
{% code-tabs-item title="client" %}
```yaml
PUT /Client/myapp

secret: verysecret
grant_types:
  - password
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Client will act on behalf of the user,  which mean Access Policies should be configured for User, not for Client.

You can configure Client for JWT tokens,  set token expiration and enable refresh token:

| attribute | options | desc |
| :--- | :--- | :--- |
| _auth.password_**.token\_format** | jwt | use access token in jwt format |
| _auth.password_**.token\_expiration** | int \(seconds\) | token expiration time from issued at |
| _auth.password_**.refresh\_token** | true/false | enable refresh\_token |
| _auth.password_**.secret\_required** | true/false | require secret for token |

## Get Access Token

Next step is collect username & password and exchange username, password, client id and secret \(if required\) for  Access Token.

 Using Basic & form-url-encode:

{% code-tabs %}
{% code-tabs-item title="using-basic" %}
```yaml
POST /auth/token
Authorization: Basic base64(client.id, client.secret)
Content-Type: application/x-www-form-urlencoded

grant_type=password&username=user&password=password
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Or by JSON request:

{% code-tabs %}
{% code-tabs-item title="json-request" %}
```yaml
POST /auth/token
Content-Type: application/json

{
  "grant_type": "password",
  "client_id": "myapp",
  "client_secret": "verysecret",
  "username": "user",
  "password": "password"
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

When everything is accurate, you will get response with access token, user information and refresh token \(if enabled\):

{% code-tabs %}
{% code-tabs-item title="token-response" %}
```yaml
status: 200

{
 "token_type": "Bearer",
 "expires_in": 3600,
 "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJodHRwOi8vbG9jYWxob3N0OjgwODEiLCJzdWIiOiJ1c2VyIiwiaWF0IjoxNTU0NDczOTk3LCJqdGkiOiI0ZWUwZDY2MS0wZjEyLTRlZmItOTBiOS1jY2RmMzhlMDhkM2QiLCJhdWQiOiJodHRwOi8vcmVzb3VyY2Uuc2VydmVyLmNvbSIsImV4cCI6MTU1NDQ3NzU5N30.lCdwkqzFWOe4IcXPC1dIB8v7aoZdJ0fBoIKlzCRFBgv4YndSJxGoJOvIPq2rGMQl7KG8uxGU0jkUVlKxOtD8YA",
 "refresh_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJodHRwOi8vbG9jYWxob3N0OjgwODEiLCJzdWIiOiJwYXNzd29yZC1jbGllbnQiLCJqdGkiOiI0ZWUwZDY2MS0wZjEyLTRlZmItOTBiOS1jY2RmMzhlMDhkM2QiLCJ0eXAiOiJyZWZyZXNoIn0.XWHYpw0DysrqQqMNhqTPSdNamBM4ZDUAgh_VupSa7rkzdJ3uZXqesoAo_5y1naJZ31S92-DjPKtPEAyD_8PloA"
 "userinfo": {
  "email": "user@mail.com",
  "id": "user",
  "resourceType": "User",
 },
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Use Access Token

You can use access token in Authorization header for Aidbox API calls:

{% code-tabs %}
{% code-tabs-item title="authorized-request" %}
```yaml
GET /Patient
Authorization: Bearer ZjQyNGFhY2EtNTY2MS00NjVjLWEzYmEtMjIwYjFkNDI5Yjhi
```
{% endcode-tabs-item %}
{% endcode-tabs %}

```text
curl -H 'Authorization: Bearer ZjQyNGFhY2EtNTY2MS00NjVjLWEzYmEtMjIwYjFkNDI5Yjhi' /Patient
```

### Revoke Access Token \(Close Session\)

Aidbox create  Session \(resource\) for each Access Token, which can be closed with special endpoint `DELETE /Session` with token in Authorization header:

{% code-tabs %}
{% code-tabs-item title="close-session" %}
```yaml
DELETE /Session
Authorization: Bearer ZjQyNGFhY2EtNTY2MS00NjVjLWEzYmEtMjIwYjFkNDI5Yjhi
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Session is just Resource and you can inspect and manipulate with sessions by standard Search & CRUD API for example get all sessions - `GET /Session`

### Auth Sandbox Demo

{% embed url="https://www.youtube.com/watch?v=\_joSWjtakmA" %}





