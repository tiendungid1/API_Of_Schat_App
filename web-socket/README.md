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

2. To create new 2G, well:

-   I have imitated a few things of Messenger (Facebook), so that if someone wants to chat with another, they don't need to be friends. But that user still needs to search for others first.

-   So, emit the **Search users and groups** event first to get the user information, and then emit the **Create new private group chat** event.

**If you have any ideas to improve this, send those ideas to our chat group :D**

3. To create new 3G, similar to creating new 2G, first get information of members by emitting the **Search users and groups** event, then emit **Create new group chat** event.

_The difference here is you need to create a new 3G before start group messaging, while if you want to message in private with someone, just find he/she and send message directly._

4. To search for users or groups or both, emit the **Search users and groups** event.

---

## Socket events

### Terms

-   Emit: Send event and data to the server.
-   On: Listen to event and data sent from the server.

### User authentication:

**Emit**

`socket.emit('authentication:validatingUser', accessToken);`

`accessToken: String (with prefix Bearer)`

If you save all the information of user to local storage (with key 'user'), then the _accessToken_ may be like this:
`Bearer ${JSON.parse(localStorage.getItem('user')).accessToken}`.

**On**

```
socket.on('authentication:errorWhenValidatingUser', error => {
    // Your code
    ...
});
```

### Online:

**Emit**

`socket.emit('online', email);`

`email: String`

### Get all conversations data:

**On**

```
socket.on('conversations:gettingAll', dto => {
    // Your code
    ...
});

socket.on('conversations:errorWhenGettingAll', error => {
    // Your code
    ...
});
```

_Note_: You should have some ways to save this data to use later. Check **Help** section for my way.

### Group messaging:

**Emit**

`socket.emit('conversations:groupMessaging', dto);`

```
dto {
    roomId: String,
    room: String,
    sender: String,
    content: String
}
```

**On**

```
socket.on('conversations:groupMessaging', dto => {
    // Your code
    ...
});

socket.on('conversations:errorWhenGroupMessaging', error => {
    // Your code
    ...
});
```

### Private messaging:

**Emit**

`socket.emit('conversations:privateMessaging', dto);`

```
dto {
    roomId: String (The room id),
    room: String (The room name),
    sender: String (Username),
    content: String (The text message)
}
```

**On**

```
socket.on('conversations:privateMessaging', dto => {
    // Your code
    ...
});

socket.on('conversations:errorWhenPrivateMessaging', error => {
    // Your code
    ...
});
```

### Create new private group chat:

**Emit**

`socket.emit('conversations:privateMessaging', dto);`

```
dto {
    sender: String (User email),
    receiver: String (User email),
    content: String (The text message)
}
```

**On**

```
socket.on('conversations:privateMessaging', dto => {
    // Emit this event
    socket.emit('socketRoom:join', dto.name);

    // Your code
    ...
});

socket.on('conversations:errorWhenPrivateMessaging', error => {
    ...
});
```

### Create new group chat:

**Emit**

`socket.emit('conversations:creatingNewGroup', dto);`

```
dto {
    name: String (Group name),
    members: Array<String> (User ids),
    createdBy: String (Email of the creator)
}
```

**On**

```
socket.on('conversations:creatingNewGroup', dto => {
    // Emit this event
    socket.emit('socketRoom:join', dto.name);

    // Your code
    ...
});

socket.on('conversations:errorWhenCreatingNewGroup', error => {
    ...
});
```

### Search users and groups:

**Emit**

`socket.emit('users-and-conversations:searching', infoWantToSearch, searchTerm);`

```
infoWantToSearch: String (Choose 1 of 'user', 'room', 'all')
searchTerm: String
```

**On**

```
socket.on('users-and-conversations:searching', dto => {
    // Your code
    ...
});

socket.on('users-and-conversations:errorWhenSearching', error => {
    // Your code
    ...
});
```

---

## Help
