Web Socket Playback Information API
-----------------------------------

Google Play Music Desktop Player provides an interface for external
applications to determine the currently played song and playback status.

This interface is provided through a locally hosted Web Socket.  In a standard
release this WebSocket is hosted on port `5672`.  Any site or application should
be able to connect to `ws://localhost:5672` and use the API.

## Message Format

All messages are sent from the Web Socket Server in the following format

```js
{
  "channel": "channel_name",
  "payload": { ...data }
}
```

## Channels

### PlayState

Data received in the `playState` channel will have a payload in the format

```js
"payload": Boolean
```

The Boolean value will be true if a song is currently playing, and false if a
song is currently paused or stopped.

### Song

Data received in the `song` channel will have a payload in the format

```js
"payload": {
  "title": "Song Title",
  "artist": "Song Artist",
  "album": "Song Album",
  "albumArt": "URL pointing to the Album Art"
}
```

This data is sent every time the currently playing song changes.  It is important
to note that this channel will still be sent even if the song changes while the
player is paused

### Lyrics

Data received in the `lyrics` channel will have a payload in the format

```js
"payload": "String of song lyrics here"
```

This data is sent every time the currently playing song changes.  It is important
to note that this channel will still be sent even if the song changes while the
player is paused.  
Also important to note that when the song first changes this
channel will be sent with a "null" value.  Once we have determined the lyrics
for the new song, the lyrics will then be sent down this channel.  You must therefore
handle that brief period of time where lyrics is null.

### Time

Data received in the `time` channel will have a payload in the format

```js
"payload": {
  "current": Number,
  "total": Number
}
```

The `current` value is the time progressed through the current song in milliseconds.  
The `total` value is the total time in milliseconds available to play in the current
song.

This channel is sent on average every 100 - 200 milliseconds.  You **will** receive a lot of data.

## Controlling the application

### All Powerful Controller

You can use **ANY** method from the `gmusic-utils` library --> https://github.com/gmusic-utils/gmusic.js

All you need to do is send a message to the websocket with a stringified JSON object in the form.

```js
{
  "namespace": "playback",
  "method": "setPlaybackTime",
  "arguments": [10000]
}
```
 Detailed object requirements
| Attribute |
