## Warning: embed3 is a work in progress

```html
<!DOCTYPE html>
<html>
    <head>
        <script src="https://ninchat.com/stage/app/embed3.js"></script>
    </head>
    <body>
        <div id="ninchat-container"></div>
        <script>
            const Ninchat = new NinchatEmbed()

            const ninchatConfig = {
                configKey: SITE_CONFIG_KEY,
                containerId: 'ninchat-container',
            }

            Ninchat.on(Ninchat.Event.ActivityCount, (data) => {console.info('Activity from Ninchat', data)})
            Ninchat.on(Ninchat.Event.Route, (data) => {console.info('Route change from Ninchat', data)})
            Ninchat.on(Ninchat.Event.Error, (data) => {console.error('Error from Ninchat', data)})

            Ninchat.init(ninchatConfig)
        </script>
    </body>
</html>
```
### Site config JSON
```json
{
    "description": "Site config",
    "default": {}
}
```

## Enums

[Ninchat.Event](#events)

| Enum value                                                  |
| ----------------------------------------------------------- |
| [`ActivityCount`](#ninchateventactivitycount)               |
| [`ContainerInitialized`](#ninchateventcontainerinitialized) |
| [`Error`](#ninchateventerror)                               |
| [`Notification`](#ninchateventnotification)                 |
| [`Route`](#ninchateventroute)                               |


Ninchat.ErrorType

When received event is [`Ninchat.Event.Error`](#ninchateventerror), 
the `error_type` property is one of the following: 

| Enum value               |
| ------------------------ |
| `ContainerNotFoundError` |
| `SiteConfigFetchError`   |
| `InvalidConfigKeyError`  |
| `InvalidSiteConfigError` |


Ninchat.NotificationType

When received event is [`Ninchat.Event.Notification`](#ninchateventnotification), 
the `type` property is one of the following: 

| Enum value                       |
| -------------------------------- |
| `AudienceRegistered`             |
| `MessageReceivedChannel`         |
| `MessageReceivedChannelAudience` |
| `MessageReceivedChannelJoin`     |
| `MessageReceivedDialogue`        |
| `QueueUpdatedAudienceAcceptPeer` |
| `QueueUpdatedAudienceRequest`    |


Ninchat.RouteName

When received event is [`Ninchat.Event.Route`](#ninchateventroute), 
the `route.name` property is one of the following: 

| Enum value          |
| ------------------- |
| `Channel`           |
| `Else`              |

## Events

One can subscribe to Ninchat events via `on` function, eg.:
`Ninchat.on(Ninchat.Event.Error, (data) => {console.error('Error from Ninchat', data)})`

Unsubscribe happens by calling `off`

Currently, the following events are supported:

#### Ninchat.Event.ActivityCount
```typescript
{
    event: Ninchat.Event.ActivityCount
    activity: number
    highlights: number
}
```
Typical use case for `ActivityCount` is to display activity as a number in host sites favicon.
`activity` property contains the total activity including `highlights`.

`highlights` are activity which has importance based on users settings or their role.
Eg. highlight can be a name mention or a visitor requesting an audience.

#### Ninchat.Event.ContainerInitialized
```typescript
{
    event: Ninchat.Event.ContainerInitialized
}
```

#### Ninchat.Event.Error
```typescript
{
    error_type: string
    event: Ninchat.Event.Error
}
```

#### Ninchat.Event.Notification
```typescript
{
    event: Ninchat.Event.Notification
}
```

#### Ninchat.Event.Route
```typescript
{
    event: Ninchat.Event.Route
    route: AudienceRoute | ChannelRoute | ElseRoute
}
```
`Route` event is emited whenever users route in Ninchat is changed. Host site can distinquish between different routes based on 
the passed properties. `metadata` in `AudienceRoute` contains pre-whitelisted subset of audience customers metadata.

##### AudienceRoute
```typescript
{
    audience_id: AudienceID
    name: 'Channel'
    metadata?: {}
    params: {
        channel_id: ChannelID
    }
}
```
##### ChannelRoute
```typescript
{
    name: 'Channel'
    params: {
        channel_id: ChannelID
    }
}
```
##### ElseRoute
```typescript
{
    name: 'Else'
}
```
