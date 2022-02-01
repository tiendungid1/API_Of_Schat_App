# WebSocket API (Using socket.io)

---

## Client installation:

Check for _standalone build_ or _CDN (ver 4.4.0)_ at https://socket.io/docs/v4/client-installation/

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

3. If the authentication is successful, server will emit the **Get all conversations data** event and send data to client.

4. Listen to all other web socket events in the list below:

-   Group messaging
-   Private messaging
-   Start new private chat
-   Create new group chat
-   Search users and groups
-   Search messages
-   Update group name
-   Leave group
-   Delete group

### The other events

1. To chat in 3G or 2G, just emit the **Group messaging** or **Private messaging** event.

2. To create new 2G, well:

-   I have imitated a few things of Messenger (Facebook), so that if someone wants to chat with another, they don't need to be friends. But that user still needs to search for others first.

-   So, emit the **Search users and groups** event first to get the user information, and then emit the **Create new private group chat** event.

> If you have any ideas to improve this, send those to our chat group :D

3. To create new 3G, similar to creating new 2G, first get information of members by emitting the **Search users and groups** event, then emit **Create new group chat** event.

> The difference here is you need to create a new 3G before start group messaging, while if you want to message in private with someone, just find him/her and send message directly.

4. To search for users or groups or both, emit the **Search users and groups** event.

## Events

### User authentication:

**Emit**

`socket.emit('event:authenticateUser', accessToken);`

`accessToken: String (with prefix Bearer)`

If you save all the information of user to local storage (with key _'user'_), then the _accessToken_ may be like this:

```
Bearer ${JSON.parse(localStorage.getItem('user')).accessToken}
```

**On**

```
socket.on('event:authenticateUser', response => {
    // Your code
    ...
});
```

> Check **Error response** section (bullet 1) for error.

### Get all conversations data:

**On**

```
socket.on('event:getAllConversations', response => {
    // Your code
    ...
});
```

> Check **Error response** section (bullet 3) for error.

> If this user is not in any groups, response will be like this:

```
{
    success: true,
    data: {
        msg: 'This user is not in any conversations',
    }
}
```

> If ok:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

> You should have some ways to save this data to use later. Check **Help** section for my way.

### Group messaging:

**Emit**

`socket.emit('event:groupMessaging', dto);`

```
dto {
    roomId: String (The room id),
    room: String (The room name),
    senderEmail: String (Email of user),
    content: String (The text message)
}
```

**On**

```
socket.on('event:groupMessaging', response => {
    // Your code
    ...
});
```

> Check **Error response** section (bullet 2, 4, 5, 6) for error.

> If ok:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

### Private messaging:

**Emit**

`socket.emit('event:privateMessaging', dto);`

```
dto {
    roomId: String (The room id),
    room: String (The room name),
    senderEmail: String (Email of user),
    content: String (The text message)
}
```

**On**

```
socket.on('event:privateMessaging', response => {
    // Your code
    ...
});
```

> Check **Error response** section (bullet 2, 4, 5, 6) for error.

> If ok:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

### Start new private chat:

**Emit**

`socket.emit('event:privateMessaging', dto);`

```
dto {
    senderEmail: String (User email),
    receiverEmail: String (User email),
    content: String (The text message)
}
```

**On**

```
socket.on('event:privateMessaging', response => {
    // If response is valid, remember to emit the "Join new socket room" event in dependent events section
    ...

    // Your code
    ...
});
```

> Check **Error response** section (bullet 4, 6) for error.

> Valid response:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

### Create new group chat:

**Emit**

`socket.emit('event:createNewGroup', dto);`

```
dto {
    name: String (Group name),
    memberEmails: Array<String> (Email of members, include the creator),
    senderEmail: String (Email of the creator)
}
```

**On**

```
socket.on('event:createNewGroup', response => {
    // If response is valid, remember to emit the "Join new socket room" event in dependent events section
    ...

    // Your code
    ...
});
```

> Check **Error response** section (bullet 4, 6) for error.

> Valid response:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

### Search users and groups: (this event has not been fixed yet, do not implement it)

**Emit**

`socket.emit('event:searchUsersAndGroups', infoWantToSearch, searchTerm);`

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

> Check the **Resources** section for sample dto.

### Search messages

**Emit**

```
socket.emit('event:searchMessages', dto);
```

```
dto {
    term: String (Search term),
    roomId: String (The room id),
    senderEmail: String (User email)
}
```

**On**

```
socket.on('event:searchMessages', response => {
    // Your code
    ...
});
```

> Check **Error response** section (bullet 4, 8) for error.

> Valid response:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

### Update group name

**Emit**

```
socket.emit('event:updateGroupName', dto);
```

```
dto {
    newName: String (New group name),
    roomId: String (The room id),
    senderEmail: String (User email)
}
```

**On**

```
socket.on('event:updateGroupName', response => {
    // If response is valid, remember to emit the "Rejoin socket room" event in dependent events section
    ...

    // Your code
    ...
});
```

> Check **Error response** section (bullet 4, 9) for error.

> Valid response:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

### Leave group

**Emit**

```
socket.emit('event:leaveGroup', dto);
```

```
dto {
    room: String (Group name),
    roomId: String (The room id),
    senderEmail: String (User email)
}
```

**On**

```
socket.on('event:leaveGroup', response => {
    // Your code
    ...
});
```

> Check **Error response** section (bullet 4, 10) for error.

> Valid response:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

### Delete group

**Emit**

```
socket.emit('event:deleteGroup', dto);
```

```
dto {
    room: String (Group name),
    roomId: String (The room id),
    senderEmail: String (User email)
}
```

**On**

```
socket.on('event:deleteGroup', response => {
    // If response is valid, remember to emit the "Leave deleted group" event in dependent events section
    ...

    // Your code
    ...
});
```

> Check **Error response** section (bullet 4, 10, 11) for error.

> Valid response:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

## Dependent events

Client will emit these events only when certain server emit certain events.

### Join new socket room:

**Emit**

```
socket.emit('event:joinNewSocketRoom', dto);
```

```
dto {
    roomId: String (The room id),
    room: String (The room name),
}
```

**On**

```
socket.on('event:joinNewSocketRoom', response => {
    // Your code
    ...
});
```

> Error response:

```
{
    success: false,
    msg: 'You can not join non-existent room',
    code: 'BAD_REQUEST',
    status: 400
}
```

> Valid response:

```
{
    success: true,
}
```

### Rejoin socket room:

**Emit**

```
socket.emit('event:rejoinSocketRoom', dto);
```

```
dto {
    roomId: String (The room id),
    oldRoom: String (The room name),
    newRoom: String (The room name),
}
```

**On**

```
socket.on('event:rejoinSocketRoom', response => {
    // Your code
    ...
});
```

> Error response:

```
{
    success: false,
    msg: 'You can not join non-existent room',
    code: 'BAD_REQUEST',
    status: 400
}
```

> Valid response:

```
{
    success: true,
}
```

### Leave deleted group

**Emit**

```
socket.emit('event:leaveDeletedGroup', dto);
```

```
dto {
    roomId: String (The room id),
    room: String (The room name),
    senderEmail: String (User email)
}
```

**On**

```
socket.on('event:leaveDeletedGroup', response => {
    // Your code
    ...
});
```

> Error response: Below + bullet 4, 10 in **Error response** section

```
{
    success: false,
    msg: 'You can not join non-existent room',
    code: 'BAD_REQUEST',
    status: 400
}
```

> Valid response:

```
{
    success: true,
}
```

## Resources

### Sample all conversations data of user

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

### Sample new message data

```
{
    "messageId": "61ebd7f66f8864dbd021b76d",
    "room": "61eaa47b70021f3e5ef37e15",
    "sender": "Tien Dung",
    "content": "tin nhan moi",
    "reaction": [],
    "reply": "...", // This key will not appear, unless this message is a reply of another message
    "createdAt": "2022-01-22T10:09:58.340Z",
}
```

### Sample new 2G data

```
{
    "name": "0arkf",
    "id": "61ebf7aae845716c199ca170",
    "members": [
        {
            "name": "Tien Dung",
            "email": "tiendung@gmail.com"
        },
        {
            "name": "Minh Dong",
            "email": "minhdong@gmail.com"
        }
    ],
    "messages": [
        {
            "sender": "Tien Dung",
            "content": "e cu",
            "createdAt": "2022-01-22T12:25:14.849Z"
        }
    ]
}
```

### Sample new 3G data

```
{
    "name": "Team Nhay",
    "id": "61ebf8cb14d5fdcf31e9bb5d",
    "members": [
        {
            "name": "Tien Dung",
            "email": "tiendung@gmail.com"
        },
        {
            "name": "Tuan Hung",
            "email": "tuanhung@gmail.com"
        },
        {
            "name": "Tran Thi Hien",
            "email": "hientran@gmail.com"
        }
    ]
}
```

### Sample search users data

```
// Search term: 'hien'

[
    {
        "id": "61ebf85dd0491e2b8980ff76",
        "email": "hientran@gmail.com",
        "name": "Tran Thi Hien",
    }
]
```

### Sample search group data

```
// Search term: 'team'

[
    "61ebf8cb14d5fdcf31e9bb5d",
    "61ebf85dd0491e2b8980ff71",
    "61ebf85dd0491e2b8980ff6f",
    "61ebf85dd0491e2b8980ff70"
]
```

### Sample search all data

```
// Search term: 'tien'

{
    "users": [
        {
            "id": "61ebf85dd0491e2b8980ff72",
            "email": "tiendung@gmail.com",
            "name": "Tien Dung",
        }
    ],
    "rooms": []
}
```

## Error response

1. Authenticate user failed:

```
{
    success: false,
    msg: 'Your access token is not valid',
    code: 'UNAUTHORIZED',
    status: 401
}
```

2. All events will have these errors if the dto send to server have problem:

```
When empty dto {
    success: false,
    msg: 'Empty data transfer object',
    code: 'BAD_REQUEST',
    status: 400
}

When missing properties {
    success: false,
    msg: 'Missing properties in data object',
    code: 'BAD_REQUEST',
    status: 400,
    missingProperties: [...]
}

When wrong email format {
    success: false,
    msg: 'Bad request',
    code: 'BAD_REQUEST',
    status: 400,
    emailErrors: [...]
}

When values is not string {
    success: false,
    msg: 'Some values of properties in data transfer object is not string',
    code: 'BAD_REQUEST',
    status: 400,
    nonStringValues: [...]
}
```

3. Server get error while getting user's groups chat and messages of groups from database:

```
{
    success: false,
    msg: "Getting internal error when trying to get user's chat groups",
    code: 'INTERNAL',
    status: 500
}

{
    success: false,
    msg: "Getting internal error when trying to get messages of chat groups",
    code: 'INTERNAL',
    status: 500
}
```

4. The email sent to server is not existed, or server get error while getting user information:

```
{
    success: false,
    msg: 'This account does not exist',
    code: 'BAD_REQUEST',
    status: 400,
}

{
    success: false,
    msg: 'Getting internal error while getting user information',
    code: 'INTERNAL',
    status: 500,
}
```

5. When user try to send message to the group that is not his/her:

```
{
    success: false,
    msg: 'You do not have permission to send message to this chat group',
    code: 'FORBIDDEN',
    status: 403,
}
```

6. Server get error while creating new message or new group in database:

```
{
    success: false,
    msg: 'Getting internal error while trying to create new document',
    code: 'INTERNAL',
    status: 500,
}
```

7. Server get error while searching for users or groups:

```
{
    success: false,
    msg: 'Getting internal error while trying to search for users',
    code: 'INTERNAL',
    status: 500,
}

{
    success: false,
    msg: 'Getting internal error while trying to search for groups',
    code: 'INTERNAL',
    status: 500,
}
```

8. Error when user search messages in group:

```
{
    success: false,
    msg: 'You do not have permission to search messages in this chat group',
    code: 'FORBIDDEN',
    status: 403,
}

{
    success: false,
    msg: 'Getting internal error while trying to search for messages',
    code: 'INTERNAL',
    status: 500,
}
```

9. Error when user change group name:

```
{
    success: false,
    msg: 'You do not have permission to change the name of this chat group',
    code: 'FORBIDDEN',
    status: 403,
}

{
    success: false,
    msg: 'Getting internal error while trying to update group name',
    code: 'INTERNAL',
    status: 500,
}
```

10. Error when user leave group:

```
{
    success: false,
    msg: 'You can not leave this group when you still are its admin',
    code: 'BAD_REQUEST',
    status: 400,
}

{
    success: false,
    msg: 'You do not have permission to leave this group',
    code: 'FORBIDDEN',
    status: 403,
}

{
    success: false,
    msg: "Getting internal error while trying to update user's group chat",
    code: 'INTERNAL',
    status: 500,
}

{
    success: false,
    msg: 'Getting internal error while trying to delete member from group',
    code: 'INTERNAL',
    status: 500,
}
```

11. Error when user delete group:

```
{
    success: false,
    msg: 'You do not have permission to delete this group',
    code: 'FORBIDDEN',
    status: 403,
}

{
    success: false,
    msg: 'Getting internal error while trying to delete room',
    code: 'INTERNAL',
    status: 500,
}

{
    success: false,
    msg: "Getting internal error while trying to update user's group chat",
    code: 'INTERNAL',
    status: 500,
}
```

## Help
