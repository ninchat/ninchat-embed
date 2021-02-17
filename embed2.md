Examples on how to embed Ninchat to a web page using site configs. There are two different use cases for embedding:

1. Queued 1-on-1 customer service chat
2. Multiple user chat room(s)

Concepts and terminology
========================

Site config key
---------------
Site config key (configKey) is a key to a JSON config file that contains configuration for the embedded chat window.

Config key format is eg. **site/0h5sa6tl/3p4137im00ud.json** which corresponds to a config file https://api.ninchat.com/config/site/0h5sa6tl/3p4137im00ud.json

Every time an embedded Ninchat is loaded the configuration of the window is constructed by Ninchat following these steps:

1. A default site config is loaded from https://ninchat.com/asset/ninchat-defaults.json
2. A site config is loaded based on configKey parameter and merged on top of default config (eg. from here: https://api.ninchat.com/config/site/0h5sa6tl/3p4137im00ud.json)
3. Configs are loaded from configUrls or postConfigUrls if set, and merged on top of the current config
4. Environment specific configs are merged on top of current config if one or more environments are set
5. Pseudo environment configs are merged on top of current config if applicable (eg. "responsive")
6. Voilà

Environment
-----------
Environment is a part of the site config used for a specific purpose.

If you take a look at the Ninchat default config (https://ninchat.com/asset/ninchat-defaults.json) you will notice two keys on top level of the configuration called `default` and `responsive`. These are environments. Environments can be overriden in a site config. Also additional environments can be added if required.

For example, if you would like to have a different config for Finnish and English users, you could add `fi` and `en` environments to the site config and set different languages for each environment. Then when initializing Ninchat you would add `fi` or `en` to the environments array based on the language selection of you users.

Pseudo-environment
------------------
A pseudo-environment is an environment that is automatically added to the environments array based on config settings. Currently there are following pseudo environments:

- `reactive` environent is added if `proactive` environment is not added. Thus the presence of `reactive` environment indicates that the chat window has been opened by user interaction and not by and automatic trigger. 

- `proactive` environment is added in if a chat window is opened by an automatic trigger as in following scenarios:
    1. `pageloads` trigger is set in a config and triggered in a chat window. In this example chat window is configured to open after 5 pageloads:
        ```json
        {
            "default": {
                "window": {
                    "triggers": [
                        {
                            "action": "open",
                            "amount": 5,
                            "type": "pageloads"
                        }
                    ]
                }
            }
        }
        ```
    2. `timeout` trigger is set in a config and triggered in a chat window. In this example chat window is configured to open after 30 seconds:
        ```json
        {
            "default": {
                "window": {
                    "triggers": [
                        {
                            "action": "open",
                            "amount": 30,
                            "type": "timeout"
                        }
                    ]
                }
            }
        }
        ```

- `responsive` environment is added in following scenarios:
    1. `responsive` is a boolean and set to true and browser client width is <= 800 pixels
        ```json
        {
            "default": {
                "window": {
                    "responsive": true
                }
            }
        }
        ```
    2. `responsive` is an integer and browser client width is <= 600 pixels
        ```json
        {
            "default": {
                "window": {
                    "responsive": 600
                }
            }
        }
        ```

configUrls and postConfigUrls
-----------------------------
- `configUrls` is an array containing a list of URLs where a GET request is made expecting a config file in return. Using configUrls a 3rd party config server can be used on top of Ninchat site configs.

- `postConfigUrls` is an array containing a list of URLs where a POST request is made expecting a config file in return. Post configs are however utilized **only after chat window is open**, which means that chat window look and feel cannot be configured in post configs.

Obtaining site config key
=========================

Config key can be obtained by following these steps:

1. Log in to Ninchat (https://ninchat.com/app)
2. Select organization name on the sidebar
3. Select "Organization settings"
4. Select "Sites"
5. Select "Create new site"

If you don't have an organization in Ninchat, please go to https://ninchat.com/contact to obtain one.
If you don't have "Organization settings" visible to you, you need to contact your organization owner for permissions.

Loading and initialization
==========================

You can load Ninchat to an existing block level element on the page (usually the chat room) or let the script create a floating element to bottom right corner of the screen (customer service). You will need to create an initialization function called `NinchatAsyncInit` and include the required JavaScript file which will call the said function after loading.


Example: Embed Ninchat using site config key
--------------------------------------------

Load the JavaScript and asynchronously initialize Ninchat to a generated floating element:

	<script type="text/javascript">
		window.NinchatAsyncInit = function() {
			window.Ninchat.embedInit({
        		configKey: 'YOUR_CONFIG_KEY'
	        });
        };

		(function(doc) {
			if (doc.getElementById('ninchat-js')) {
				return;
			}

			var js, first = doc.getElementsByTagName('script')[0];

			js     = doc.createElement('script');
			js.id  = 'ninchat-js';
			js.src = 'https://ninchat.com/js/embed2.min.js';
			first.parentNode.insertBefore(js, first);
		}(document));
	</script>

Load the JavaScript and initialize Ninchat to a pre-defined element on page:

	<div id="ninchat-iframe" style="width: 300px; height: 400px;"></div>
    
	<script type="text/javascript">
		window.NinchatAsyncInit = function() {
			window.Ninchat.embedInit({
				configKey: 'YOUR_CONFIG_KEY',
				containerId: 'ninchat-iframe'
			});
		};

		(function(doc) {
			if (doc.getElementById('ninchat-js')) {
				return;
			}

			var js, first = doc.getElementsByTagName('script')[0];

			js     = doc.createElement('script');
			js.id  = 'ninchat-js';
			js.src = 'https://ninchat.com/js/embed2.min.js';
			first.parentNode.insertBefore(js, first);
		}(document));
	</script>

Initialization options
----------------------

Default value is `false` or `null` unless otherwise spesified.

### Top level options

- `configKey` : string

	Site config key to be used for initializing. [Read more about site config key](#site-config-key)

- `configUrls` : array (optional)

	List of URL addresses for self-hosted configs. [Read more about configUrls](#configurls-and-postconfigurls)

- `containerId` : string, default: `ninchat-iframe`
	
	DOM element id for the chat container. If using the default value you don't need to give `containerId` in the public API calls.

- `description`: string
    
	Descriptive text of the config file displayed in the site config list in "Sites" view under Organization settings.

- `default`: object
    
	Default environment. All config options are placed under an environment in the config

- `postConfigUrls` : array (optional)

	List of URL addresses for self-hosted configs loaded after chat is opened. [Read more about postConfigUrls](#configurls-and-postconfigurls)

- `responsive`: object
    
	Pseudo-environment for responsive chat window

    ```json
    {
      "containerId": "ninchat-container",
      "description": "Site config for Ninchat embed example",
      "default": {
        "motd": "Message of the day for customer care chat window."
      },
      "responsive": {
        "motd": "Message of the day for small screens."
      }
    }
    ```

### Generic options

- `autoStart` : boolean, default: `true`
	
	Start chat automatically. If set to `false` you must use [`Ninchat.start()`](#ninchat-start) to start chat, can be used to start customer service chat on demand.
	
- `cookiePrefix` : string, default: `ninchat_`
	
	Prefix used when storing chat state on client browser. Mainly for customer service, saves to chat popup horizontal position and open/close state.
	
- `css` : string
	
	URL for custom CSS file included in the chat.

- `debug` : boolean
	
	Show debug data in console.

- `domain` : string, defaults to the parent site hostname
	
	Used to identify user credentials if `remember` is set to `true`. If multiple chats are embedded on the same page you may need to override this with e.g. `expert.domain.tld` and `guest.domain.tld`.

- `externalStyles` : array
    
	A list containing https URLs to external CSS files loaded to the window hosting embedded chat  

- `guestOnlyLogin` : boolean
	
	Disable login with existing Ninchat or Facebook user accounts.

- `hideCommand` : boolean
	
	Hide the input field.
	
- `hideTitle` : boolean, default: `true`
	
	Hide the channel/private conversation title bar.

- `language` : string
	
	Change the client language, available values are `fi` for Finnish and `en` for English (default).

- `masterKeyType` : string
	
	Specifies the type of the signature/token used in `userMasterSign` and
	`channelMasterSign`; "ninchat" or "jwt".

- `remember` : boolean, default: `true`
	
	Remember the user after page reload.

- `reverseBacklog` : boolean
	
	Backlog in reverse order with the input field at top.

- `translations` : object
	
	Over-ride translations, e.g.:
		{
			"Join audience queue {{audienceQueue.queue_attrs.name}}": "Join {{audienceQueue.queue_attrs.name}}",
			"Join audience queue {{audienceQueue.queue_attrs.name}} (closed)": "{{audienceQueue.queue_attrs.name}} is closed"
		}
		
- `userAuth` : string
	
	User authentication code for logging in a known user, `userId` is required if used.
	
- `userId` : string
	
	User id for logging in a known user, `userAuth` is required if used.
	
- `userMasterSign` : string
	
	A cryptographic signature/token used when creating session.  May be used
	e.g. for login authorization.

- `userName` : string
	
	Name to be used in chat. The name will be prompted when trying to send first message if not given.

- `window` : object
    
	Chat window UI configuration. See "Window configuration"

### Channel embed specific options

- `channelId` : string
    
	The channel id where we land after loading chat.

- `channelMasterSign` : string
	
	A cryptographic signature/token used when joining channels.  May be used
	e.g. to join a non-public channel.

### Private conversation (=dialogue) embed specific options
	
- `dialogueUserId` : string
	
	Like channelId, but open a dialogue with the specified user instead of
	joining a channel.

### Customer service (=audience) embed specific options
	
- `audienceRealmId` : string
	
	The realm id used for customer service queues.
	
- `audienceQueues` : string array
	
	Comma separated list of visible customer service queues, list all if not set.

- `audienceAutoQueue` : string
	
	Go to this queue automatically, if it's open.  Otherwise the list of open
    queues is shown.  If you don't want to show other queues, set
    audienceQueues to a list containing only this queue, and set noQueuesText.

- `audienceRating` : boolean
	
	Enable customer service satisfaction rating.

- `audienceMetadata` : object
	
	Arbitrary strings to be displayed to the customer service agent at the start of the dialogue.

- `inQueueText` : string
	
	Shown in place of `motd` after the user has entered a customer service
	queue.  Defaults to `motd`.  May contain HTML.

- `motd` : string
	
	Message of the day text displayed below the customer service queues. May contain HTML.
	
- `noQueuesText` : string
	
	Text displayed when no open customer service queues are available.

### Window configuration

When Ninchat is embedded in a default container, a floating chat window is automatically rendered. Window configuration allows customization of different aspects of the chat window.

Chat window has following possible states:

- `hidden` Window is not visible at all
- `minimized` Only title bar of the window is visible
- `open` Window is open and visible
- `full` Window is open and its height is 100%

Window configuration is located under `window` in each environment and has the following options:

- `ball` : object

	Configuration for the view when `dock` is set to `ball`.

- `dock` : string, default: `bottom`

	Determines how window is docked. Available options are `ball`, `bottom`, `left`, `right` or `top`.

- `dockOffset` : string, default: `0px 10px`

	Offset of the chat window. `0px 10px` would mean the window is placed `0px` away from bottom and `10px` away from right when docked to bottom.

- `initialState` : string, default: `minimized`

	Initial state of the window.

- `modes` : object

	Configuration for different window modes. Available keys are `default`, `open` and `full`.

- `promobubble` : object

	Configuration for a bubble shown next to chat window promoting the chat.

- `responsive` : boolean|integer, default: `true`

	If true (or custom breakpoint pixel value), container will have a `responsive` pseudo-environment if screen width below 800 pixels.

- `sidebar` : object

	Configuration for chat window "tab" when `dock` is set to `left` or `right`.

- `triggers` : string array, default: `[]`

	List of triggers applied to chat window.


Public API
----------

### `Ninchat.channel`

- `channelId` : string
- `containerId` : string (optional)

Change to another channel, will join/follow if necessary.


### `Ninchat.close`

- `containerId` : string (optional)
- `forceClose` : boolean (optional)

Close floating chat window and send delete_user (if guest), same as clicking &times; or "Close chat" button. When conversation is ongoing, close confirmation is presented. When confirmed, audience rating and/or post audience questionnaire are displayed if `audienceRating` or `postAudienceQuestionnaire` are set.

If `forceClose` is set to `true`, chat is closed and user is deleted: no confirmations, ratings or questionnaires are displayed. Possible scenario for using `forceClose` could be during logout process on embedding site. 


### `Ninchat.dialogue`

- `userId` : string
- `containerId` : string (optional)

Open a private conversation with the specified user.


### `Ninchat.earlyMetadata`

- `data` : object

Add or replace parts of the initial `audienceMetadata` object.  Returns a
boolean; true is returned if Ninchat hasn't been started yet (i.e. the initial
metadata could still be modified).

The [`Ninchat.metadata`](#ninchatmetadata) function may be used to send
metadata to the chat after Ninchat has already been started.


### `Ninchat.embedInit`

- `_options` : object

Initialize embedded chat, see available options in [Initialization options](#initialization-options).


### `Ninchat.metadata`

- `data` : object
- `fold` : boolean (optional)

Send audience metadata message to the customer care agent.  The messages may be
interleaved with other messages in the chat.

The data object contains arbitrary string keys mapped to string values.  If
fold is true, the values override previous values of the keys.  Fold is false
by default.

As opposed to [`Ninchat.earlyMetadata`](#ninchatearlymetadata), this function
works at any time.


### `Ninchat.start`

- `containerId` : string (optional)

Start chat if `autoStart` is set to `false`.


### `Ninchat.toggle`

- `open` : boolean (optional)
- `containerId` : string (optional)

Toggle floating window open state.


Configuration examples
======================

Bare minumum customer service chat
----------------------------------

```json
{
  "description": "Bare minumum customer service chat",
  "default": {
	"audienceQueues": [ "QUEUE_ID_HERE" ],
	"audienceRealmId": "REALM_ID_HERE"
  }
}
```

This document is subject to changes.
