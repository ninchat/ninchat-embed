Examples on how to embed Ninchat to a web page.  There are two different use cases for embedding:

1. Queued 1-on-1 customer service chat
2. Multiple user chat room(s)


Loading and initialization
==========================

You can load Ninchat to an existing block level element on the page (usually the chat room) or let the script create a floating element to bottom right corner of the screen (customer service). You will need to create an initialization function called `NinchatAsyncInit` and include the required JavaScript file which will call the said function after loading.


Example: Customer service
-------------------------

Load the JavaScript and asynchronously initialize a chat bar to bottom right:

	<script type="text/javascript">
		window.NinchatAsyncInit = function() {
			window.Ninchat.embedInit({
        			audienceRealmId: 'YOUR_REALM_ID',
			        titleText: 'Customer service',
			        motd: 'Welcome to <strong>ACME</strong> customer service'
	        	});
        	};

		(function(doc) {
			if (doc.getElementById('ninchat-js')) {
				return;
			}

			var js, first = doc.getElementsByTagName('script')[0], date = new Date();

			js     = doc.createElement('script');
			js.id  = 'ninchat-js';
			js.src = 'https://ninchat.com/js/embed.min.js?_=' + (date.getMonth() * 100 + date.getDate());
			first.parentNode.insertBefore(js, first);
		}(document));
	</script>


Example: Chat room
------------------

Load the JavaScript and add the chat room to pre-defined element on page:

	<div id="ninchat-iframe" style="width: 300px; height: 400px;"></div>
    
	<script type="text/javascript">
		window.NinchatAsyncInit = function() {
			window.Ninchat.embedInit({
				channelId: 'YOUR_CHANNEL_ID',
				containerId: 'ninchat-iframe'
			});
		};

		(function(doc) {
			if (doc.getElementById('ninchat-js')) {
				return;
			}

			var js, first = doc.getElementsByTagName('script')[0], date = new Date();

			js     = doc.createElement('script');
			js.id  = 'ninchat-js';
			js.src = 'https://ninchat.com/js/embed.min.js?_=' + (date.getMonth() * 100 + date.getDate());
			first.parentNode.insertBefore(js, first);
		}(document));
	</script>


Example: Multiple chat rooms
----------------------------

Show two different chat rooms, e.g. one where only experts are allowed to talk and guests can follow the discussion and another where guests are allowed to talk:

	<div id="ninchat-experts" style="width: 300px; height: 400px; float: left;"></div>
	<div id="ninchat-guests" style="width: 300px; height: 400px; float: left;"></div>
    
	<script type="text/javascript">
		window.NinchatAsyncInit = function() {
			window.Ninchat.embedInit({
				channelId: 'YOUR_EXPERT_CHANNEL_ID',
				containerId: 'ninchat-experts',
				hideCommand: true,
				remember: false
			});
			window.Ninchat.embedInit({
				channelId: 'YOUR_GUEST_CHANNEL_ID',
				containerId: 'ninchat-guests'
			});
		};

		(function(doc) {
			if (doc.getElementById('ninchat-js')) {
				return;
			}

			var js, first = doc.getElementsByTagName('script')[0], date = new Date();

			js     = doc.createElement('script');
			js.id  = 'ninchat-js';
			js.src = 'https://ninchat.com/js/embed.min.js?_=' + (date.getMonth() * 100 + date.getDate());
			first.parentNode.insertBefore(js, first);
		}(document));
	</script>



Initialization options
----------------------

Default value is `false` or `null` unless otherwise spesified.


### Generic options

- `autoStart` : boolean, default: `true`

	Start chat automatically. If set to `false` you must use [`Ninchat.start()`](#ninchat-start) to start chat, can be used to start customer service chat on demand.
	
- `audienceRealmId` : string

	The realm id used for customer service queues.
	
- `audienceQueues` : string array

	Comma separated list of visible customer service queues, list all if not set.

- `audienceAutoQueue` : string

	Go to this queue automatically, if it's open.  Otherwise the list of open
    queues is shown.  If you don't want to show other queues, set
    audienceQueues to a list containing only this queue, and set noQueuesText.

- `audienceRating` : boolean

	Enable customer service satisfaction rateing.

- `audienceMetadata` : object

	Arbitrary strings to be displayed to the customer service agent at the start of the dialogue.

- `channelId` : string

	The channel id where we land after loading chat.

- `cookiePrefix` : string, default: `ninchat_`

	Prefix used when storing chat state on client browser. Mainly for customer service, saves to chat popup horizontal position and open/close state.
	
- `debug` : boolean

	Show debug data in console.
	
- `dialogueUserId` : string

	Like channelId, but open a dialogue with the specified user instead of
	joining a channel.

- `domain` : string, defaults to the parent site hostname

	Used to identify user credentials if `remember` is set to `true`. If multiple chats are embedded on the same page you may need to override this with e.g. `expert.domain.tld` and `guest.domain.tld`.
	
- `guestOnlyLogin` : boolean

	Disable login with existing Ninchat or Facebook user accounts.

- `hideCommand` : boolean

	Hide the input field.
	
- `hideTitle` : boolean, default: `true`

	Hide the channel/private conversation title bar.
	
- `inQueueText` : string

	Shown in place of `motd` after the user has entered a customer service
	queue.  Defaults to `motd`.  May contain HTML.

- `language` : string

	Change the client language, available values are `fi` for Finnish and `en` for English (default).

- `longpoll` : boolean

	Force long poll connection instead of websocket.

- `masterKeyType` : string

	Specifies the type of the signature/token used in `userMasterSign`;
	"ninchat" or "jwt".

- `motd` : string

	Message of the day text displayed below the customer service queues. May contain HTML.
	
- `noQueuesText` : string

	Text displayed when no open customer service queues are available.

- `open` : boolean

	Open chat automatically after loading. Used with customer service chat.
	
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


### UI options
	
- `containerId` : string, default: `ninchat-iframe`

	DOM element id for the chat container. If using the default value you don't need to give `containerId` in the public API calls.

- `css` : string

	URL for custom CSS file included in the chat.

- `dock` : string, default: `bottom`

	Dock floating chat window to `top` or `bottom`.

- `height` : string, default: `400px`

	Customer service chat window height.

- `responsive` : boolean|number

	If true (or custom breakpoint pixel value), container will take max width (or height) if screen width below 800 pixels.

- `titleBackground` : string, default: `black`

	Background color for the title bar when using customer service mode.

- `titleText` : string, default: `Audience`

	Text for the title bar.
	
- `titleTextColor` : string, default: `white`

	Color for the title bar text.

- `width` : string, default: `240px`

	Customer service chat window width.
	


Public API
----------

### `Ninchat.channel`

- `channelId` : string
- `containerId` : string (optional)

Change to another channel, will join/follow if necessary.


### `Ninchat.close`

- `containerId` : string (optional)

Close floating chat window and send delete_user, same as clicking &times;.


### `Ninchat.dialogue`

- `userId` : string
- `containerId` : string (optional)

Open a private conversation with the specified user.


### `Ninchat.embedInit`

- `_options` : object

Initialize embedded chat, see available options in [Initialization options](#initialization-options).


### `Ninchat.iframeInit`

- `_options` : object

Re-initialize the iframe, see available options in [Initialization options](#initialization-options). Can be used for example to login with another user.


### `Ninchat.metadata`

- `data` : object
- `fold` : boolean (optional)

The data object contains arbitrary string keys mapped to string values.  If
fold is true, the values override previous values of the keys.  Fold is false
by default.


### `Ninchat.rename`

- `name` : string
- `containerId` : string (optional)

Rename current user in container.


### `Ninchat.start`

- `containerId` : string (optional)

Start chat if `autoStart` is set to `false`.


### `Ninchat.toggle`

- `open` : boolean (optional)
- `containerId` : string (optional)

Toggle floating window open state.


This document is subject to changes.
