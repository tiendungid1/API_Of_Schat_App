# WebSocket API (Using socket.io)

---

## Client installation:

Check for _standalone build_ or _CDN (ver 4.4.0)_ at https://socket.io/docs/v4/client-installation/

---

## Instructions

### Abbreviation:

-   3G: Chat group with more then 2 members.
-   2G: Chat group with just 2 members.

### When the page load

1. After user has successfully logged in and redirected to home page, init the client when the page load:

```
const socket = io();
```

2. Emit the **User authentication** event and send the access token to the server.

3. If the authentication is successful, emit the **Online** event and send the email of user to the server.

4. To get all the conversations of user, emit the **Get all conversations data** event.

5. After completing 4 steps above, listen to all other web socket events in the list below (from **Group messaging** to **Search users and groups**).

### The other events

1. To chat in 3G or 2G, just emit the **Group messaging** or **Private messaging** event.

2. To create new 3G, emit the **Create new group chat** event.

3. To create new 2G, well:

-   I have imitated a few things of Messenger (Facebook), so that if someone wants to chat with another, they don't need to be friends of each other. But that user still needs to search for others first.

-   So, emit the **Search users and groups** event first to get the user information, and then emit the **Create new private group chat** event.

**If you have any ideas to improve this, send those ideas to our chat group :D**

4. To search for users or group chats or both, emit the **Search users and groups** event.

---

## Socket events

### Terms

-   Emit: Send event and data to the server.
-   On: Listen to event and data sent from the server.

### User authentication:

**Emit**

`socket.emit('authentication:validatingUser', accessToken);`

`accessToken: Type String (with prefix Bearer)`

-   If you save all the information of user to local storage (with key 'user'), then the _accessToken_ may be like this:
    `Bearer ${JSON.parse(localStorage.getItem('user')).accessToken}`

**On**

```
socket.on('authentication:errorWhenValidatingUser', error => {
    ...
});
```

### Online:

**Emit**

`socket.emit('online', email);`

`email: Type String`

### Get all conversations data:

### Group messaging:

### Private messaging:

### Create new private group chat:

### Create new group chat:

### Search users and groups:
