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

            const options = {
                configKey: SITE_CONFIG_KEY,
                containerId: 'ninchat-container',
            }

            Ninchat.on(Ninchat.Event.ActivityCount, (data) => {console.info('Activity from Ninchat', data)})
            Ninchat.on(Ninchat.Event.Route, (data) => {console.info('Route change from Ninchat', data)})
            Ninchat.on(Ninchat.Event.Error, (data) => {console.error('Error from Ninchat', data)})

            Ninchat.init(options)
        </script>
    </body>
</html>
```
### Site config JSON
```json
{
    "description": "Site config",
    "default": {
        "variant": "app"
    }
}
```

## Init options

| Key                | Type              | Description                                                                          |
| :----------------- | :---------------- | :----------------------------------------------------------------------------------- |
| `audienceMetadata` | object (optional) | Audience metadata passed to the expert.                                              |
| `config`           | object (optional) | Config used to override subset of the keys obtained via configKey and others.        |
| `configKey`        | string            | Site config key to be used for initializing.                                         |
| `configUrls`       | array (optional)  | List of URL addresses for self-hosted configs.                                       |
| `containerId`      | string            | DOM element id for where the chat container is placed.                               |
| `postConfigUrls`   | array (optional)  | List of URL addresses for self-hosted configs loaded via POST request.               |
| `environment`      | array (optional)  | List of environment keys. 'default' is always used, and can be omited.               |

## Functions

#### Ninchat.destruct
Removes all event listeners and de-initialized Ninchat.

```es6
const onRemoveNinchat = () => {
    Ninchat.destruct()
}
```

#### Ninchat.init
Initializes Ninchat, and loads it in an iframe, which is placed inside container.

```es6
const options = {
    configKey: SITE_CONFIG_KEY,
    containerId: 'ninchat-container',
}

Ninchat.init(options)
```

#### Ninchat.off
Removes a NinchatEvent listener and its callback function.

```es6
const onNinchatErrorEvent = (data) => {
    console.error('Error from Ninchat', data)
}

Ninchat.off(Ninchat.Event.Error, onNinchatErrorEvent)
```

#### Ninchat.on
Adds a NinchatEvent listener and a callback function.

```es6
const onNinchatErrorEvent = (data) => {
    console.error('Error from Ninchat', data)
}

Ninchat.on(Ninchat.Event.Error, onNinchatErrorEvent)
```

## Enums

#### [Ninchat.Event](#events)

| Enum value                                                  |
| ----------------------------------------------------------- |
| [`ActivityCount`](#ninchateventactivitycount)               |
| [`ContainerInitialized`](#ninchateventcontainerinitialized) |
| [`Error`](#ninchateventerror)                               |
| [`Notification`](#ninchateventnotification)                 |
| [`Route`](#ninchateventroute)                               |


#### Ninchat.ErrorType

When received event is [`Ninchat.Event.Error`](#ninchateventerror), 
the `error_type` property is one of the following: 

| Enum value               |
| ------------------------ |
| `ContainerNotFoundError` |
| `SiteConfigFetchError`   |
| `InvalidConfigKeyError`  |
| `InvalidSiteConfigError` |


#### Ninchat.NotificationType

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


#### Ninchat.RouteName

When received event is [`Ninchat.Event.Route`](#ninchateventroute), 
the `route.name` property is one of the following: 

| Enum value          |
| ------------------- |
| `Channel`           |
| `Else`              |

## Events

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
    audio?: boolean
    channel_name?: string
    desktop?: boolean
    event: Ninchat.Event.Notification
    queue_name?: string
    realm_name?: string
    type: Ninchat.NotificationType
    user_name?: string
}
```
`Notification` event is emited whenever user would receive either a desktop or an audio notification 
based on users preferences. When Ninchat is embedded, instead of firing a desktop or audio notification, 
a `Notification` event is emited instead.


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
    audience_id: string
    name: Ninchat.RouteName.Channel
    metadata?: {}
    params: {
        channel_id: string
    }
}
```


##### ChannelRoute
```typescript
{
    name: Ninchat.RouteName.Channel
    params: {
        channel_id: string
    }
}
```


##### ElseRoute
```typescript
{
    name: Ninchat.RouteName.Else
}
```
