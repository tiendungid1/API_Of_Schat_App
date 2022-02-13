# WebSocket API (Using socket.io)

---

## Client installation

Check for _standalone build_ or _CDN (ver 4.4.0)_ at https://socket.io/docs/v4/client-installation/

## Instructions

### Abbreviation:

-   3G: Chat group with more then 2 members.
-   2G: Chat group with just 2 members.

### When the page load:

1. After user has successfully logged in and redirected to home page, init the client when the page load:

```
const socket = io(url);
```

> The url will be updated later.

2. Emit the **User authentication** event and send the access token to the server.

3. If the authentication is successful, server will emit the **Get all conversations data** event and send data to client.

4. Listen to all other web socket events in the list below:

-   Group messaging
-   Private messaging
-   Start new private chat
-   Set pin message
-   Reply to message
-   React to message
-   Broadcast typing (check the warning)
-   Create new group chat
-   Search users and groups
-   Search messages
-   Update group name
-   Update group thumbnail
-   Leave group
-   Delete group
-   Add members
-   Delete member
-   Grant admin privilege
-   View group files

### The other events:

1. To chat in 3G or 2G, just emit the **Group messaging** or **Private messaging** event:

-   Emit **Set pin message**, **Reply to message**, **React to message** event to perform the corresponding job.
-   To send image, you must call Upload image API first (check REST API document) to upload image to cloud and get the image URL. Then emit **Group messaging** or **Private messaging** event to send that URL to server.
-   To let others know who is typing, emit **Broadcast typing** event (please check the warning).

2. To create new 2G, well:

-   I have imitated a few things of Messenger (Facebook), so that if someone wants to chat with another, they don't need to be friends. But that user still needs to search for others first.

-   So, emit the **Search users and groups** event first to get the user information, and then emit the **Create new private group chat** event.

> If you have any ideas to improve this, send those to our chat group :D

3. To create new 3G, similar to creating new 2G, first get information of members by emitting the **Search users and groups** event, then emit **Create new group chat** event.

> The difference here is you need to create a new 3G before start group messaging, while if you want to message in private with someone, just find him/her and send message directly.

4. To search for users or groups or both, emit the **Search users and groups** event.

5. To search for messages, emit the **Search messages** event.

6. These events (**Update group name**, **Update group thumbnail**, **Leave group**, **Delete group**, **Add members**, **Delete member**, **Grant admin privilege**) can not be done if the chat group is 2G.

## Events

### User authentication:

**Emit**

```
socket.emit('event:authenticateUser', accessToken);
```

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

```
socket.emit('event:groupMessaging', dto);
```

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

> Check **Error response** section (bullet 2, 4, 7, 8) for error.

> If ok:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

### Private messaging:

**Emit**

```
socket.emit('event:privateMessaging', dto);
```

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

> Check **Error response** section (bullet 2, 4, 7, 8) for error.

> If ok:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

### Start new private chat:

**Emit**

```
socket.emit('event:privateMessaging', dto);
```

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
    // If response is valid, emit the "Join new socket room" event in dependent events section
    ...

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

### Create new group chat:

**Emit**

```
socket.emit('event:createNewGroup', dto);
```

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
    // If response is valid, emit the "Join new socket room" event in dependent events section
    ...

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

### Set pin message:

**Emit**

```
socket.emit('event:setPinMessage', dto);
```

```
dto {
    roomId: String (Group id),
    room: String (Group name),
    senderEmail: String (Email of emitter),
    messageId: String (Id of message)
}
```

**On**

```
socket.on('event:setPinMessage', response => {
    // Your code
    ...
});
```

> Check **Error response** section (bullet 2, 4, 5, 6, 9) for error.

> Valid response:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

### Reply to message:

**Emit**

```
socket.emit('event:replyToMessage', dto);
```

```
dto {
    roomId: String (Group id),
    room: String (Group name),
    senderEmail: String (Email of emitter),
    content: String (Content of new message)
    messageId: String (Id of replied message)
}
```

**On**

```
socket.on('event:replyToMessage', response => {
    // Your code
    ...
});
```

> Check **Error response** section (bullet 2, 4, 5, 6, 8, 10) for error.

> Valid response:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

### React to message:

**Emit**

```
socket.emit('event:reactToMessage', dto);
```

```
dto {
    roomId: String (Group id),
    room: String (Group name),
    senderEmail: String (Email of emitter),
    content: String (Content of react)
    messageId: String (Id of message)
}
```

> Server saves reaction as string. You should have some ways to recognize the string value and render appropriate reaction.

**On**

```
socket.on('event:reactToMessage', response => {
    // Your code
    ...
});
```

> Check **Error response** section (bullet 2, 4, 5, 6, 11) for error.

> Valid response:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

### Broadcast typing:

**Emit**

```
socket.emit('event:typing', dto);
```

```
dto {
    room: String (Group name),
    sender: String (Name of user who's typing),
}
```

**On**

```
socket.on('event:typing', response => {
    // Your code
    ...
});
```

> Valid response:

```
{
    success: true,
    data: {
        msg: `Minh Dong is typing...`
    }
}
```

> Warning: Typing, deleting some characters, typing again... Too fast and regularly. So, to ensure the speed, the server does not perform any validate. That mean using this event can cause bugs.

### Search users and groups:

**Emit**

```
socket.emit('event:searchUsersAndGroups', infoWantToSearch, dto);
```

```
infoWantToSearch: String ('user' or 'room' or 'both')

dto {
    term: String (Search term),
    senderEmail: String (User email)
}
```

**On**

```
socket.on('event:searchUsersAndGroups', response => {
    // Your code
    ...
});
```

> Check **Error response** section (bullet 2, 12) for error.

> Valid response:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

### Search messages:

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

> Check **Error response** section (bullet 2, 4, 13) for error.

> Valid response:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

### Update group name:

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
    // If response is valid, emit the "Rejoin socket room" event in dependent events section
    ...

    // Your code
    ...
});
```

> Check **Error response** section (bullet 2, 4, 14) for error.

> Valid response:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

### Update group thumbnail:

**Emit**

```
socket.emit('event:updateGroupThumbnail', dto);
```

```
dto {
    room: String (Group name),
    roomId: String (Group id),
    senderEmail: String (Email of emitter),
    thumbnail: String (Url of thumbnail)
}
```

**On**

```
socket.on('event:updateGroupThumbnail', response => {
    // Your code
    ...
});
```

> Check **Error response** section (bullet 2, 4, 5, 15) for error.

> Valid response:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

> You must call Upload image API first (check REST API document) to upload image to cloud and get the image URL. Then send that URL to server, not the image file.

### Leave group:

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

> Check **Error response** section (bullet 2, 4, 5, 16) for error.

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
    // If response is valid, emit the "Leave deleted group" event in dependent events section
    ...

    // Your code
    ...
});
```

> Check **Error response** section (bullet 2, 4, 17) for error.

> Valid response:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

### Add members

**Emit**

```
socket.emit('event:addMembers', dto);
```

```
dto {
    room: String (Group name),
    roomId: String (The room id),
    senderEmail: String (Email of emitter),
    userEmails: Array<String> (Email of new members)
}
```

> You can add one member or multiple members to group at once. If you add one, then "userEmails" is array of one element.

**On**

```
socket.on('event:addMembers', response => {
    // If response is valid, remember to emit the "Join new socket room" event in dependent events section
    ...

    // Your code
    ...
});
```

> Check **Error response** section (bullet 2, 4, 5, 18) for error.

> Valid response: Response between users who have already been members of the group and new members will be different.

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

### Delete member

**Emit**

```
socket.emit('event:deleteMember', dto);
```

```
dto {
    room: String (Group name),
    roomId: String (The room id),
    senderEmail: String (Email of emitter),
    memberEmail: String (Email of member who you want to delete)
}
```

**On**

```
socket.on('event:deleteMember', response => {
    // If response is valid, emit the Leave socket room event in dependent event section

    // Your code
    ...
});
```

> Check **Error response** section (bullet 2, 4, 5, 19) for error.

> Valid response:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

### Grant admin privilege

**Emit**

```
socket.emit('event:grantAdminPrivilege', dto);
```

```
dto {
    room: String (Group name),
    roomId: String (The room id),
    senderEmail: String (Email of emitter),
    memberEmail: String (Email of member who you want to grant admin privilege)
}
```

**On**

```
socket.on('event:grantAdminPrivilege', response => {
    // Your code
    ...
});
```

> Check **Error response** section (bullet 2, 4, 5, 20) for error.

> Valid response:

```
{
    success: true,
    data: {
        msg: 'User ... is now admin of group ...'
    }
}
```

### View group files

**Emit**

```
socket.emit('event:viewGroupFiles', fileType, dto);
```

```
fileType: String ('media' or 'file')

dto {
    roomId: String (The room id),
    senderEmail: String (Email of emitter),
}
```

> It's likely that we will not make Upload file API. It mean you can only send image to group, not files. So... the "fileType" here is just "media".

**On**

```
socket.on('event:viewGroupFiles', response => {
    // Your code
    ...
});
```

> Check **Error response** section (bullet 2, 4, 5, 21) for error.

> Valid response:

```
{
    success: true,
    data: ..., // Check the Resources section for sample data
}
```

## Dependent events

Client will emit these events only when server emit certain events.

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

> Error response: Below + bullet 2 in Error response section

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

> Error response: Below + bullet 2 in Error response section

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

### Leave deleted group:

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

> Error response: Below + bullet 2, 4 in Error response section

```
{
    success: false,
    msg: 'You can not join non-existent room',
    code: 'BAD_REQUEST',
    status: 400
}

{
    success: false,
    msg: "Getting internal error while trying to update user's group chat",
    code: 'INTERNAL',
    status: 500,
}
```

> Valid response:

```
{
    success: true,
}
```

### Leave socket room

**Emit**

```
socket.emit('event:leaveSocketRoom', dto);
```

```
dto {
    roomId: String (The room id),
    room: String (The room name),
}
```

**On**

```
socket.on('event:leaveSocketRoom', response => {
    // Your code
    ...
});
```

> Error response: Below + bullet 2 in Error response section

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

1. Sample all conversations data of user

```
[
    {
        "name": "Team BE",
        "id": "62087041921e86b45a2dd8d3",
        "thumbnail": null,
        "isPrivate": false,
        "members": [
            {
                "name": "Tien Dung",
                "email": "tiendung@gmail.com",
                "ava": null
            },
            {
                "name": "Duc Huy",
                "email": "duchuy@gmail.com",
                "ava": null
            },
            {
                "name": "Tuan Hung",
                "email": "tuanhung@gmail.com",
                "ava": null
            }
        ],
        "messages": [
            {
                "id": "62087041921e86b45a2dd8f0",
                "room": "62087041921e86b45a2dd8d3",
                "sender": "Tien Dung",
                "content": "q0kpqmcok2n",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.772Z"
            },
            {
                "id": "62087041921e86b45a2dd8f5",
                "room": "62087041921e86b45a2dd8d3",
                "sender": "Duc Huy",
                "content": "5le8nnnedel",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.772Z"
            },
            {
                "id": "62087041921e86b45a2dd8f6",
                "room": "62087041921e86b45a2dd8d3",
                "sender": "Tuan Hung",
                "content": "5uld882t696",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.772Z"
            },
            {
                "id": "62087041921e86b45a2dd8f7",
                "room": "62087041921e86b45a2dd8d3",
                "sender": "Tien Dung",
                "content": "9pmpj8iwqcb",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.773Z"
            },
            {
                "id": "62087041921e86b45a2dd8f8",
                "room": "62087041921e86b45a2dd8d3",
                "sender": "Tuan Hung",
                "content": "yfs8hl6e3gr",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.773Z"
            },
            {
                "id": "62087041921e86b45a2dd8f9",
                "room": "62087041921e86b45a2dd8d3",
                "sender": "Tien Dung",
                "content": "ayr6ww7s9wj",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.773Z"
            },
            {
                "id": "62087041921e86b45a2dd8fd",
                "room": "62087041921e86b45a2dd8d3",
                "sender": "Tuan Hung",
                "content": "kq2ynfqu8d",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.773Z"
            },
            {
                "id": "62087041921e86b45a2dd901",
                "room": "62087041921e86b45a2dd8d3",
                "sender": "Duc Huy",
                "content": "3vaw4yjjbll",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.774Z"
            },
            {
                "id": "62087041921e86b45a2dd902",
                "room": "62087041921e86b45a2dd8d3",
                "sender": "Duc Huy",
                "content": "qhg5vpqf8q",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.774Z"
            },
            {
                "id": "62087041921e86b45a2dd905",
                "room": "62087041921e86b45a2dd8d3",
                "sender": "Tuan Hung",
                "content": "khmkvx6xeh",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.774Z"
            },
            {
                "id": "62087041921e86b45a2dd907",
                "room": "62087041921e86b45a2dd8d3",
                "sender": "Tuan Hung",
                "content": "44kxbm00pmv",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.774Z"
            },
            {
                "id": "62087041921e86b45a2dd909",
                "room": "62087041921e86b45a2dd8d3",
                "sender": "Tuan Hung",
                "content": "0ht7dh7nwhjn",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.775Z"
            },
            {
                "id": "62087041921e86b45a2dd90a",
                "room": "62087041921e86b45a2dd8d3",
                "sender": "Duc Huy",
                "content": "ivs3od5euy",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.775Z"
            }
        ]
    },
    {
        "name": "Team Find Bug",
        "id": "62087041921e86b45a2dd8d4",
        "thumbnail": null,
        "isPrivate": false,
        "members": [
            {
                "name": "Tien Dung",
                "email": "tiendung@gmail.com",
                "ava": null
            },
            {
                "name": "Duc Huy",
                "email": "duchuy@gmail.com",
                "ava": null
            },
            {
                "name": "Minh Dong",
                "email": "minhdong@gmail.com",
                "ava": null
            }
        ],
        "messages": [
            {
                "id": "62087041921e86b45a2dd8ef",
                "room": "62087041921e86b45a2dd8d4",
                "sender": "Minh Dong",
                "content": "rbwwidxaji",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.771Z"
            },
            {
                "id": "62087041921e86b45a2dd8f4",
                "room": "62087041921e86b45a2dd8d4",
                "sender": "Duc Huy",
                "content": "pu15cwpvozq",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.772Z"
            },
            {
                "id": "62087041921e86b45a2dd8fa",
                "room": "62087041921e86b45a2dd8d4",
                "sender": "Duc Huy",
                "content": "bgalijcbmk",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.773Z"
            },
            {
                "id": "62087041921e86b45a2dd8fc",
                "room": "62087041921e86b45a2dd8d4",
                "sender": "Duc Huy",
                "content": "eqa7h614m8e",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.773Z"
            },
            {
                "id": "62087041921e86b45a2dd8fe",
                "room": "62087041921e86b45a2dd8d4",
                "sender": "Duc Huy",
                "content": "qdkwdbmuc5",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.773Z"
            },
            {
                "id": "62087041921e86b45a2dd8ff",
                "room": "62087041921e86b45a2dd8d4",
                "sender": "Tien Dung",
                "content": "jq93kaxu93",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.774Z"
            },
            {
                "id": "62087041921e86b45a2dd908",
                "room": "62087041921e86b45a2dd8d4",
                "sender": "Duc Huy",
                "content": "vz2zc6w52j",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.775Z"
            }
        ]
    },
    {
        "name": "Team FE",
        "id": "62087041921e86b45a2dd8d5",
        "thumbnail": null,
        "isPrivate": false,
        "members": [
            {
                "name": "Tien Dung",
                "email": "tiendung@gmail.com",
                "ava": null
            },
            {
                "name": "Minh Dong",
                "email": "minhdong@gmail.com",
                "ava": null
            },
            {
                "name": "Tran Thi Hien",
                "email": "hientran@gmail.com",
                "ava": null
            }
        ],
        "messages": [
            {
                "id": "62087041921e86b45a2dd8f1",
                "room": "62087041921e86b45a2dd8d5",
                "sender": "Tran Thi Hien",
                "content": "rsqby6p2hy",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.772Z"
            },
            {
                "id": "62087041921e86b45a2dd8f2",
                "room": "62087041921e86b45a2dd8d5",
                "sender": "Tien Dung",
                "content": "xgbh79tuzo",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.772Z"
            },
            {
                "id": "62087041921e86b45a2dd8f3",
                "room": "62087041921e86b45a2dd8d5",
                "sender": "Minh Dong",
                "content": "jbou9q7hzo",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.772Z"
            },
            {
                "id": "62087041921e86b45a2dd8fb",
                "room": "62087041921e86b45a2dd8d5",
                "sender": "Tien Dung",
                "content": "dvynt57vtf",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.773Z"
            },
            {
                "id": "62087041921e86b45a2dd900",
                "room": "62087041921e86b45a2dd8d5",
                "sender": "Tien Dung",
                "content": "3dp1h9t4feh",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.774Z"
            },
            {
                "id": "62087041921e86b45a2dd903",
                "room": "62087041921e86b45a2dd8d5",
                "sender": "Tran Thi Hien",
                "content": "can2hq15dsu",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.774Z"
            },
            {
                "id": "62087041921e86b45a2dd904",
                "room": "62087041921e86b45a2dd8d5",
                "sender": "Tien Dung",
                "content": "l6safnt4npf",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.774Z"
            },
            {
                "id": "62087041921e86b45a2dd906",
                "room": "62087041921e86b45a2dd8d5",
                "sender": "Minh Dong",
                "content": "iyt4qbpie37",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.774Z"
            },
            {
                "id": "62087041921e86b45a2dd90b",
                "room": "62087041921e86b45a2dd8d5",
                "sender": "Tien Dung",
                "content": "kes4uxkicoc",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.775Z"
            },
            {
                "id": "62087041921e86b45a2dd90c",
                "room": "62087041921e86b45a2dd8d5",
                "sender": "Tran Thi Hien",
                "content": "isqhqzi0xwh",
                "isPinned": false,
                "reaction": [],
                "createdAt": "2022-02-13T02:43:13.776Z"
            }
        ]
    }
]
```

2. Sample message data

```
{
    "id": "61ebd7f66f8864dbd021b76d",
    "room": "61eaa47b70021f3e5ef37e15",
    "sender": "Tien Dung",
    "content": "test tin nhan moi",
    "isPinned": false,
    "reaction": [],
    // "reply" will be an empty object, unless this message is a reply of another message
    "reply": {
        id: "61ebd7f66f8864dbd021b76e",
        content: "tin nhan duoc reply"
    },
    // "reaction" will be an empty array, unless this message was reacted
    "reaction": [
        {
            "sender": "Minh Dong",
            "ava": null,
            "content": "HEART"
        },
        {
            "sender": "Duc Huy",
            "ava": null,
            "content": "SAD"
        }
    ],
    "createdAt": "2022-01-22T10:09:58.340Z",
}
```

3. Sample new 2G data

```
{
    "name": "fhgj4",
    "id": "6208774818596470be32253b",
    "thumbnail": null,
    "isPrivate": true,
    "members": [
        {
            "name": "Tien Dung",
            "email": "tiendung@gmail.com",
            "ava": null
        },
        {
            "name": "Minh Dong",
            "email": "minhdong@gmail.com",
            "ava": null
        }
    ],
    "messages": [
        {
            "id": "6208774818596470be32253f",
            "room": "6208774818596470be32253b",
            "sender": "Tien Dung",
            "content": "e cu",
            "isPinned": false,
            "reply": {},
            "reaction": [],
            "createdAt": "2022-02-13T03:13:12.708Z"
        }
    ]
}
```

4. Sample new 3G data

```
{
    "name": "New Group",
    "id": "62087cd6dfa1ebaf5407c3f1",
    "thumbnail": null,
    "isPrivate": false,
    "members": [
        {
            "email": "hientran@gmail.com",
            "name": "Tran Thi Hien",
            "ava": null
        },
        {
            "email": "tiendung@gmail.com",
            "name": "Tien Dung",
            "ava": null
        },
        {
            "email": "tuanhung@gmail.com",
            "name": "Tuan Hung",
            "ava": null
        }
    ]
}
```

5. Sample search users data

```
// Search term: 'hien'

[
    {
        "email": "hientran@gmail.com",
        "name": "Tran Thi Hien",
        "ava": null,
        "rooms": ["620270e7955486282c1ad18a"]
    }
]
```

6. Sample search group data

```
// Search term: 'team'

[
    "61ebf8cb14d5fdcf31e9bb5d",
    "61ebf85dd0491e2b8980ff71",
    "61ebf85dd0491e2b8980ff6f",
    "61ebf85dd0491e2b8980ff70"
]
```

7. Sample search users and groups data

```
// Search term: 'tien'

{
    "users": [
        {
            "email": "tiendung@gmail.com",
            "name": "Tien Dung",
            "ava": null,
            "rooms": ["620270e7955486282c1ad188", "620270e7955486282c1ad189", "620270e7955486282c1ad18a"]
        }
    ],
    "rooms": []
}
```

8. Sample search messages data

```
[
    {
    "id": "61ebd7f66f8864dbd021b76d",
    "room": "61eaa47b70021f3e5ef37e15",
    "sender": "Tien Dung",
    "content": "test tin nhan moi",
    "isPinned": false,
    "reaction": [],
    // "reply" will be an empty object, unless this message is a reply of another message
    "reply": {
        id: "61ebd7f66f8864dbd021b76e",
        content: "tin nhan duoc reply"
    },
    // "reaction" will be an empty array, unless this message was reacted
    "reaction": [
        {
            "sender": "Minh Dong",
            "ava": null,
            "content": "HEART"
        },
        {
            "sender": "Duc Huy",
            "ava": null,
            "content": "SAD"
        }
    ],
    "createdAt": "2022-01-22T10:09:58.340Z",
}
]
```

9. Sample update group name data

```
{
    "roomId": "61eaa47b70021f3e5ef37e15",
    "oldRoom": "Old group"
    "newRoom": "New group",
}
```

10. Sample update group thumbnail data

```
{
    "roomId": "61eaa47b70021f3e5ef37e15",
    "thumbnail": "..."
}
```

11. Sample leave group data

```
{
    "email": "tiendung@gmail.com",
    "name": "Tien Dung",
    "ava": null,
    "rooms": ["620270e7955486282c1ad188", "620270e7955486282c1ad189", "620270e7955486282c1ad18a"]
}
```

12. Sample delete group data

```
{
    "id": "61eaa47b70021f3e5ef37e15",
    "name": "Team BE",
}
```

12. Sample add members data

```Members of group
{
    "roomId": "620270e7955486282c1ad188",
    "newMembers": [
        {
            "email": "hientran@gmail.com",
            "name": "Tran Thi Hien",
            "ava": null,
            "rooms": [
                "620270e7955486282c1ad18a"
            ]
        },
        {
            "email": "minhdong@gmail.com",
            "name": "Minh Dong",
            "ava": null,
            "rooms": [
                "620270e7955486282c1ad189",
                "620270e7955486282c1ad18a"
            ]
        }
    ]
}
```

```Member who is added
{
    "name": "Team BE",
    "id": "620270e7955486282c1ad188",
    "thumbnail": null,
    "isPrivate": false,
    "members": [
        {
            "email": "tiendung@gmail.com",
            "name": "Tien Dung",
            "ava": null,
            "rooms": [
                "620270e7955486282c1ad188",
                "620270e7955486282c1ad189",
                "620270e7955486282c1ad18a"
            ]
        },
        {
            "email": "duchuy@gmail.com",
            "name": "Duc Huy",
            "ava": null,
            "rooms": [
                "620270e7955486282c1ad188",
                "620270e7955486282c1ad189"
            ]
        },
        {
            "email": "tuanhung@gmail.com",
            "name": "Tuan Hung",
            "ava": null,
            "rooms": [
                "620270e7955486282c1ad188"
            ]
        },
        {
            "email": "hientran@gmail.com",
            "name": "Tran Thi Hien",
            "ava": null,
            "rooms": [
                "620270e7955486282c1ad18a"
            ]
        },
        {
            "email": "minhdong@gmail.com",
            "name": "Minh Dong",
            "ava": null,
            "rooms": [
                "620270e7955486282c1ad189",
                "620270e7955486282c1ad18a"
            ]
        }
    ],
    "messages": [
        {
            "id": "620270e7955486282c1ad1a5",
            "room": "620270e7955486282c1ad188",
            "sender": "Duc Huy",
            "content": "mr9fwexvfa",
            "isPinned": false,
            "reply": {},
            "reaction": [],
            "createdAt": "2022-02-08T13:32:23.961Z"
        },
        {
            "id": "620270e7955486282c1ad1a6",
            "room": "620270e7955486282c1ad188",
            "sender": "Tien Dung",
            "content": "xvqhhwvlwk",
            "isPinned": false,
            "reply": {},
            "reaction": [],
            "createdAt": "2022-02-08T13:32:23.962Z"
        },
        {
            "id": "620270e7955486282c1ad1a7",
            "room": "620270e7955486282c1ad188",
            "sender": "Tuan Hung",
            "content": "r3gm3p8ailc",
            "isPinned": false,
            "reply": {},
            "reaction": [],
            "createdAt": "2022-02-08T13:32:23.962Z"
        },
        {
            "id": "620270e7955486282c1ad1a8",
            "room": "620270e7955486282c1ad188",
            "sender": "Tien Dung",
            "content": "47rugi7lpjj",
            "isPinned": false,
            "reply": {},
            "reaction": [],
            "createdAt": "2022-02-08T13:32:23.962Z"
        },
        {
            "id": "620270e7955486282c1ad1ad",
            "room": "620270e7955486282c1ad188",
            "sender": "Tuan Hung",
            "content": "ji2mvjkkpz",
            "isPinned": false,
            "reply": {},
            "reaction": [],
            "createdAt": "2022-02-08T13:32:23.963Z"
        },
        {
            "id": "620270e7955486282c1ad1ae",
            "room": "620270e7955486282c1ad188",
            "sender": "Tien Dung",
            "content": "uzs9iqk8yeo",
            "isPinned": false,
            "reply": {},
            "reaction": [],
            "createdAt": "2022-02-08T13:32:23.963Z"
        },
        {
            "id": "620270e7955486282c1ad1af",
            "room": "620270e7955486282c1ad188",
            "sender": "Tuan Hung",
            "content": "wtp5ivczita",
            "isPinned": false,
            "reply": {},
            "reaction": [],
            "createdAt": "2022-02-08T13:32:23.963Z"
        },
        {
            "id": "620270e7955486282c1ad1b1",
            "room": "620270e7955486282c1ad188",
            "sender": "Tien Dung",
            "content": "emo4c6kq9hc",
            "isPinned": false,
            "reply": {},
            "reaction": [],
            "createdAt": "2022-02-08T13:32:23.963Z"
        },
        {
            "id": "620270e7955486282c1ad1b2",
            "room": "620270e7955486282c1ad188",
            "sender": "Tuan Hung",
            "content": "4x7ndel08jo",
            "isPinned": false,
            "reply": {},
            "reaction": [],
            "createdAt": "2022-02-08T13:32:23.963Z"
        },
        {
            "id": "620270e7955486282c1ad1b7",
            "room": "620270e7955486282c1ad188",
            "sender": "Tien Dung",
            "content": "uqm644n5db",
            "isPinned": false,
            "reply": {},
            "reaction": [],
            "createdAt": "2022-02-08T13:32:23.964Z"
        },
        {
            "id": "620270e7955486282c1ad1b8",
            "room": "620270e7955486282c1ad188",
            "sender": "Tien Dung",
            "content": "u0uy3xc76nl",
            "isPinned": false,
            "reply": {},
            "reaction": [],
            "createdAt": "2022-02-08T13:32:23.964Z"
        },
        {
            "id": "620270e7955486282c1ad1bb",
            "room": "620270e7955486282c1ad188",
            "sender": "Tuan Hung",
            "content": "jznlccoku8",
            "isPinned": false,
            "reply": {},
            "reaction": [],
            "createdAt": "2022-02-08T13:32:23.964Z"
        }
    ]
}
```

13. Sample delete member data

```
{
    "roomId": "6203783b4b3ccc1b1be453c1",
    "room": "Team BE",
    "memberEmail": "duchuy@gmail.com"
}
```

14. Sample view group files data

```
["url1", "url2", "url3"]
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

4. Get user info:

```
{
    success: false,
    msg: 'This account does not exist',
    code: 'BAD_REQUEST',
    status: 400,
}

{
    success: false,
    msg: 'Some accounts do not exist',
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

5. Get group info:

```
{
    success: false,
    msg: 'This chat group does not exist',
    code: 'BAD_REQUEST',
    status: 400,
}

{
    success: false,
    msg: 'Getting internal error while trying to get chat group info',
    code: 'INTERNAL',
    status: 500,
}
```

6. Get message info:

```
{
    success: false,
    msg: 'This message does not exist',
    code: 'BAD_REQUEST',
    status: 400,
}

{
    success: false,
    msg: 'Getting internal error when trying to get message info',
    code: 'INTERNAL',
    status: 500,
}
```

7. User send message to the group that is not his/her:

```
{
    success: false,
    msg: 'You do not have permission to send message to this chat group',
    code: 'FORBIDDEN',
    status: 403,
}
```

8. Create new message or new group in database:

```
{
    success: false,
    msg: 'Getting internal error while trying to create new document',
    code: 'INTERNAL',
    status: 500,
}
```

9. Set pin message:

```
{
    success: false,
    msg: 'You do not have permission to set pin message for this group',
    code: 'FORBIDDEN',
    status: 403,
}

{
    success: false,
    msg: 'The message you want to pin is not in this group',
    code: 'BAD_REQUEST',
    status: 400,
}

{
    success: false,
    msg: 'Getting internal error while trying to set pin message',
    code: 'INTERNAL',
    status: 500,
}
```

10. Reply to message:

```
{
    success: false,
    msg: 'The message you want to reply to is not in this group',
    code: 'BAD_REQUEST',
    status: 400,
}
```

11. React to message:

```
{
    success: false,
    msg: 'You do not have permission to react to message in this group',
    code: 'FORBIDDEN',
    status: 403,
}

{
    success: false,
    msg: 'The message you want to react to is not in this group',
    code: 'BAD_REQUEST',
    status: 400,
}

{
    success: false,
    msg: 'Getting internal error while trying to set reaction for message',
    code: 'INTERNAL',
    status: 500,
}
```

12. Search for users or groups:

```
{
    success: false,
    msg: 'Missing info type',
    code: 'BAD_REQUEST',
    status: 400,
}

{
    success: false,
    msg: "Info type must be 'user' or 'room' or 'both'",
    code: 'BAD_REQUEST',
    status: 400,
}

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

13. Search messages in group:

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

14. Change group name:

```
{
    success: false,
    msg: 'You can not change the private group name',
    code: 'BAD_REQUEST',
    status: 400,
}

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

15. Change group thumbnail:

```
{
    success: false,
    msg: 'You can not change the private group thumbnail',
    code: 'BAD_REQUEST',
    status: 400,
}

{
    success: false,
    msg: 'You do not have permission to change the thumbnail of this chat group',
    code: 'FORBIDDEN',
    status: 403,
}

{
    success: false,
    msg: 'Getting internal error while trying to update group thumbnail',
    code: 'INTERNAL',
    status: 500,
}
```

16. Leave group:

```
{
    success: false,
    msg: 'You can not leave a private group',
    code: 'BAD_REQUEST',
    status: 400,
}

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

17. Delete group:

```
{
    success: false,
    msg: 'You can not delete a private group',
    code: 'BAD_REQUEST',
    status: 400,
}

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

18. Add members to group:

```
{
    success: false,
    msg: 'You can not add members to the private group',
    code: 'BAD_REQUEST',
    status: 400,
}

{
    success: false,
    msg: 'You do not have permission to add members to this group',
    code: 'FORBIDDEN',
    status: 403,
}

{
    success: false,
    msg: 'Some users have already been in this group',
    code: 'BAD_REQUEST',
    status: 400,
    existedMembers: [...]
}

{
    success: false,
    msg: 'Getting internal error when trying to update group chat of users',
    code: 'INTERNAL',
    status: 500,
}

{
    success: false,
    msg: 'Getting internal error when trying to add members to group',
    code: 'INTERNAL',
    status: 500,
}
```

19. Delete member:

```
{
    success: false,
    msg: 'You can not delete member from a private group',
    code: 'BAD_REQUEST',
    status: 400,
}

{
    success: false,
    msg: 'You do not have permission to delete member in this group',
    code: 'FORBIDDEN',
    status: 403,
}

{
    success: false,
    msg: 'You can not delete this member since he/she is not in this group',
    code: 'BAD_REQUEST',
    status: 400,
}

{
    success: false,
    msg: 'Getting internal error while trying to delete member from group',
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

20. Grant admin privilege

```
{
    success: false,
    msg: 'You can not grant admin privilege within a private group',
    code: 'BAD_REQUEST',
    status: 400,
}

{
    success: false,
    msg: 'You have already been an admin of this group',
    code: 'BAD_REQUEST',
    status: 400,
}

{
    success: false,
    msg: 'You do not have permission to grant admin privilege to member in this group',
    code: 'FORBIDDEN',
    status: 403,
}

{
    success: false,
    msg: 'You can not grant admin privilege to this member since he/she is not in this group',
    code: 'FORBIDDEN',
    status: 403,
}

{
    success: false,
    msg: "Getting internal error while trying to change group's admin",
    code: 'INTERNAL',
    status: 500,
}
```

21. View group files

```
{
    success: false,
    msg: 'Missing file type',
    code: 'BAD_REQUEST',
    status: 400,
}

{
    success: false,
    msg: "File type must be 'media' or 'file'",
    code: 'BAD_REQUEST',
    status: 400,
}

{
    success: false,
    msg: 'You do not have permission to view files in this group',
    code: 'FORBIDDEN',
    status: 403,
}

{
    success: false,
    msg: 'Getting internal error while trying to get images in group',
    code: 'INTERNAL',
    status: 500,
}
```

## Helper

### About store group data to use later:

1. You can create a Map (check here: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) with key is **id** of group, and value is the group data. You can use this function to convert data (in bullet 1 of Resources section) to a map:

```
function createMap(arr) {
    return new Map(arr.map(el => [el.id, el]));
}
```

> In some events, I only send back the group ID. So using Map is highly recommended, as it will only cost O(1) to get the group data from the group ID.

2. To convert a map to array:

```
function arrayFromMap(map) {
    return Array.from(map.values());
}
```

3. If you want to sort the group when there are new message (from latest to oldest), you can use this function:

```
function sortRoomsByMessagesDateCreated(data) {
    return data.sort((a, b) => {
        if (
            a.messages.length !== 0 &&
            b.messages.length !== 0
        ) {
            return (
                Date.parse(b.messages[b.messages.length - 1].createdAt) -
                Date.parse(a.messages[a.messages.length - 1].createdAt)
            );
        }
    });
}
```

> This function can sort array only. Beware if you are using Map.

### About message:

You can use `new Date(message.createdAt)` to convert date string to date object.

## Bugs

I have killed a lot of bugs but maybe there are still many left. Anyway, these two bugs below are big bugs, but I'm too lazy to fix them :v

1. [BIG BOSS] Normally, the user information related to a message will be his/her avatar and name. However, if someone leaves the group or is deleted, then all the messages sent by that user will lose information about his/her avatar. So the client should rendering a random picture to avoid error.

2. [FINAL BOSS] There are many places in code that have not been handled transaction well. This is quite serious because if an _Internal server error_ occurs during the operation with a group, that group will **die** (can not interact will it as usual).
