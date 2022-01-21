# REST API

---

## User login API

-   Method: POST
-   Route: /users/login
-   Base path: api
-   Full URL example: http://localhost:8443/api/users/login

### Request body (required)

```
{
    email: String,
    password: String
}
```

_Example request body_

```
{
    email: "duchuy@gmail.com",
    password: "123456"
}
```

### Response

_Successful operation_

```
{
    email: String,
    name: String,
    accessToken: String
}
```

_Example response data_

```
{
    email: "duchuy@gmail.com",
    name: "Duc Huy",
    accessToken: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjYxZWFhNDdiNzAwMjFmM2U1ZWYzN2UxOCIsImlhdCI6MTY0Mjc2ODgyNCwiZXhwIjoxNjQyODU1MjI0fQ.x-pO_Fj48xiz-HWI86SI6Ijxdcsm0GZ2V3C8R5jkMng"
}
```

### Errors

_Failed operation: Wrong email_

```
{
    code: "BAD_REQUEST",
    message: "This account does not exist",
    status: 400
}
```

_Failed operation: Wrong password_

```
{
    code: "UNAUTHORIZED",
    message: "Your current password is incorrect",
    status: 401
}
```

_Failed operation: Wrong email format_

```
{
    code: "BAD_REQUEST",
    detail: [
        {
            message: "\"email\" must be a valid email",
            type: "string.email"
        }
    ],
    message: "Bad request",
    status: 400
}
```

_Failed operation: Wrong password format_

```
{
    code: "BAD_REQUEST",
    detail: [
        {
            message: "\"password\" with value \"...\" fails to match the required pattern: /^[a-zA-Z0-9\\d@$!%*?&]{6,30}$/",
            type: "string.pattern.base"
        }
    ],
    message: "Bad request",
    status: 400
}
```

## Reset password API

-   Method: PATCH
-   Route: /users/reset-password
-   Base path: api
-   Full URL example: http://localhost:8443/api/users/reset-password

### Request body (required)

```
{
    email: String,
    newPassword: String
}
```

_Example request body_

```
{
    email: "duchuy@gmail.com",
    newPassword: "123456"
}
```

### Response

If successful, no response data. Or maybe a status code 204 will be send back (HTTP Status 204 No Content indicates that the server has successfully fulfilled the request and that there is no content to send in the response payload body).

### Errors

_Failed operation: Wrong email_

```
{
    code: "BAD_REQUEST",
    message: "This account does not exist",
    status: 400
}
```

_Failed operation: Wrong email format_

```
{
    code: "BAD_REQUEST",
    detail: [
        {
            message: "\"email\" must be a valid email",
            type: "string.email"
        }
    ],
    message: "Bad request",
    status: 400
}
```

_Failed operation: Wrong password format_

```
{
    code: "BAD_REQUEST",
    detail: [
        {
            message: "\"password\" with value \"...\" fails to match the required pattern: /^[a-zA-Z0-9\\d@$!%*?&]{6,30}$/",
            type: "string.pattern.base"
        }
    ],
    message: "Bad request",
    status: 400
}
```
