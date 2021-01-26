# LiveKit Server API for JS

Javascript/Typescript APIs to manage rooms and to create access tokens. This library is designed to work with [livekit-server](https://github.com/livekit/livekit-server). Use it with a Node.js backend to manage access to LiveKit.

## Installation

### Yarn

```
yarn add livekit-server-api
```

### NPM

```
npm install livekit-server-api --save
```

## Usage

### Environment Variables

You may store credentials in environment variables. If api-key or api-secret is not passed in when creating a `RoomServiceClient` or `AccessToken`, the values in the following env vars will be used:

- `LIVEKIT_API_KEY`
- `LIVEKIT_API_SECRET`

### Creating Access Tokens

Creating a token for participant to join a room.

```typescript
import { AccessToken } from 'livekit-server-api';

// if this room doesn't exist, it'll be automatically created when the first
// client joins
const roomName = 'name-of-room';
// identifier to be used for participant.
// it's available as LocalParticipant.identity with livekit-client SDK
const participantName = 'user-name';

const at = new AccessToken('api-key', 'secret-key', {
  identity: participantName,
});
at.addGrant({ room: roomName });

const token = at.toJwt();
console.log('access token', token);
```

By default, the token expires after 6 hours. you may override this by passing in `ttl` in the access token options. `ttl` is expressed in seconds (as number) or a string describing a time span [vercel/ms](https://github.com/vercel/ms). eg: '2 days', '10h'.

### Managing Rooms

`RoomServiceClient` gives you APIs to list, create, and delete rooms. It also requires a pair of api key/secret key to operate.

```typescript
import { RoomServiceClient, Room } from 'livekit-server-api';
const livekitHost = 'https://my.livekit.host';
const svc = new RoomServiceClient(livekitHost, 'api-key', 'secret-key');

// list rooms
svc.listRooms().then((rooms: Room[]) => {
  console.log('existing rooms', rooms);
});

// create a new room
const opts = {
  name: 'myroom';
  // timeout in seconds
  emptyTimeout: 10 * 60;
  maxParticipants: 20;
}
svc.createRoom(opts).then((room: Room) => {
  console.log('room created', room)
})

// delete a room
svc.deleteRoom('myroom').then(() => {
  console.log('room deleted')
})
```