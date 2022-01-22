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

> If you have any ideas to improve this, send those ideas to our chat group :D

3. To create new 3G, similar to creating new 2G, first get information of members by emitting the **Search users and groups** event, then emit **Create new group chat** event.

> The difference here is you need to create a new 3G before start group messaging, while if you want to message in private with someone, just find him/her and send message directly.

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

If you save all the information of user to local storage (with key _'user'_), then the _accessToken_ may be like this:
`Bearer ${JSON.parse(localStorage.getItem('user')).accessToken}`

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

`email: String (User email)`

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

> You should have some ways to save this data to use later. Check **Help** section for my way.

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
    members: Array<String> (Member ids, include the creator),
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
infoWantToSearch: String (Choose one of 'user', 'room', 'all')
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

## Resources

### Sample conversations data of user (sent from server)

```
[
    {
        "name": "Team BE",
        "id": "61eaa47b70021f3e5ef37e15",
        "members": [
            {
                "name": "Tien Dung",
                "email": "tiendung@gmail.com"
            },
            {
                "name": "Duc Huy",
                "email": "duchuy@gmail.com"
            },
            {
                "name": "Tuan Hung",
                "email": "tuanhung@gmail.com"
            }
        ],
        "messages": [
            {
                "sender": "Duc Huy",
                "content": "5gst0acbn4h",
                "createdAt": "2022-01-21T12:18:03.278Z"
            },
            {
                "sender": "Tien Dung",
                "content": "0g4d6blxfi6q",
                "createdAt": "2022-01-21T12:18:03.278Z"
            },
            {
                "sender": "Tuan Hung",
                "content": "0fm48kdstvza",
                "createdAt": "2022-01-21T12:18:03.278Z"
            },
            {
                "sender": "Duc Huy",
                "content": "xx0wuvx6mcj",
                "createdAt": "2022-01-21T12:18:03.279Z"
            },
            {
                "sender": "Duc Huy",
                "content": "iq0xpgc51dg",
                "createdAt": "2022-01-21T12:18:03.279Z"
            },
            {
                "sender": "Duc Huy",
                "content": "f4aye39ywr5",
                "createdAt": "2022-01-21T12:18:03.280Z"
            },
            {
                "sender": "Tuan Hung",
                "content": "hqn9p9vmuaw",
                "createdAt": "2022-01-21T12:18:03.280Z"
            },
            {
                "sender": "Tien Dung",
                "content": "75657i723o",
                "createdAt": "2022-01-21T12:18:03.281Z"
            },
            {
                "sender": "Tien Dung",
                "content": "dzw4k48hok9",
                "createdAt": "2022-01-21T12:18:03.281Z"
            },
            {
                "sender": "Tuan Hung",
                "content": "920zby5hsr",
                "createdAt": "2022-01-21T12:18:03.281Z"
            },
            {
                "sender": "Duc Huy",
                "content": "pg0zkum8rkg",
                "createdAt": "2022-01-21T12:18:03.282Z"
            },
            {
                "sender": "Duc Huy",
                "content": "b48h1gmd8g",
                "createdAt": "2022-01-21T12:18:03.283Z"
            },
            {
                "sender": "Tien Dung",
                "content": "v0xy9a4tihc",
                "createdAt": "2022-01-21T12:18:03.283Z"
            }
        ],
        "pinMessages": []
    },
    {
        "name": "Team Find Bug",
        "id": "61eaa47b70021f3e5ef37e16",
        "members": [
            {
                "name": "Tien Dung",
                "email": "tiendung@gmail.com"
            },
            {
                "name": "Duc Huy",
                "email": "duchuy@gmail.com"
            },
            {
                "name": "Minh Dong",
                "email": "minhdong@gmail.com"
            }
        ],
        "messages": [
            {
                "sender": "Tien Dung",
                "content": "22l0zn5ekwy",
                "createdAt": "2022-01-21T12:18:03.276Z"
            },
            {
                "sender": "Duc Huy",
                "content": "9rlx37ba9zo",
                "createdAt": "2022-01-21T12:18:03.277Z"
            },
            {
                "sender": "Minh Dong",
                "content": "gtl41gwiogn",
                "createdAt": "2022-01-21T12:18:03.277Z"
            },
            {
                "sender": "Minh Dong",
                "content": "5o8xpntggnn",
                "createdAt": "2022-01-21T12:18:03.277Z"
            },
            {
                "sender": "Minh Dong",
                "content": "t0dy9m1trcc",
                "createdAt": "2022-01-21T12:18:03.278Z"
            },
            {
                "sender": "Tien Dung",
                "content": "pixcuoqnihq",
                "createdAt": "2022-01-21T12:18:03.282Z"
            },
            {
                "sender": "Minh Dong",
                "content": "kz0s9n4r7a",
                "createdAt": "2022-01-21T12:18:03.282Z"
            },
            {
                "sender": "Duc Huy",
                "content": "l42nyw9lech",
                "createdAt": "2022-01-21T12:18:03.283Z"
            }
        ],
        "pinMessages": []
    },
    {
        "name": "Team FE",
        "id": "61eaa47b70021f3e5ef37e17",
        "members": [
            {
                "name": "Tien Dung",
                "email": "tiendung@gmail.com"
            },
            {
                "name": "Minh Dong",
                "email": "minhdong@gmail.com"
            },
            {
                "name": "Tran Thi Hien",
                "email": "hientran@gmail.com"
            }
        ],
        "messages": [
            {
                "sender": "Tran Thi Hien",
                "content": "nfw81pwfbkd",
                "createdAt": "2022-01-21T12:18:03.275Z"
            },
            {
                "sender": "Tien Dung",
                "content": "1uzawijk3d5",
                "createdAt": "2022-01-21T12:18:03.278Z"
            },
            {
                "sender": "Tran Thi Hien",
                "content": "1stkfugwqju",
                "createdAt": "2022-01-21T12:18:03.279Z"
            },
            {
                "sender": "Minh Dong",
                "content": "8nll2yqf6tv",
                "createdAt": "2022-01-21T12:18:03.279Z"
            },
            {
                "sender": "Tran Thi Hien",
                "content": "oqn2u9a59hb",
                "createdAt": "2022-01-21T12:18:03.280Z"
            },
            {
                "sender": "Tien Dung",
                "content": "ph78jyj1t0g",
                "createdAt": "2022-01-21T12:18:03.281Z"
            },
            {
                "sender": "Tran Thi Hien",
                "content": "haeou0bpzsh",
                "createdAt": "2022-01-21T12:18:03.282Z"
            },
            {
                "sender": "Tien Dung",
                "content": "z560cbse0y",
                "createdAt": "2022-01-21T12:18:03.282Z"
            },
            {
                "sender": "Minh Dong",
                "content": "785yvnff9wl",
                "createdAt": "2022-01-21T12:18:03.284Z"
            }
        ],
        "pinMessages": []
    }
]
```

### Sample new message data (sent from server)

```
{
    "deletedAt": null,
    "room": "61eaa47b70021f3e5ef37e15",
    "sender": "Tien Dung",
    "content": "tin nhan moi",
    "_id": "61ebd7f66f8864dbd021b76d",
    "reaction": [],
    "createdAt": "2022-01-22T10:09:58.340Z",
    "updatedAt": "2022-01-22T10:09:58.340Z",
    "__v": 0
}
```

---

## Help
