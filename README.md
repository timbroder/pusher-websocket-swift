# PusherSwift (pusher-websocket-swift)

[![Build Status](https://travis-ci.org/pusher/pusher-websocket-swift.svg?branch=master)](https://travis-ci.org/pusher/pusher-websocket-swift)

## Table of Contents

* [Installation](#installation)
* [Configuration](#configuration)
* [Connection](#connection)
* [Subscribing to channels](#subscribing)
* [Binding to events](#binding-to-events)
  * [Globally](#global-events)
  * [Per-channel](#per-channel-events)
* [Testing](#testing)
* [Communication](#communication)
* [Credits](#credits)
* [License](#license)


## Installation

### CocoaPods

[CocoaPods](http://cocoapods.org) is a dependency manager for Cocoa projects and is our recommended method of installing PusherSwift and its dependencies.

If you don't already have the Cocoapods gem installed, run the following command:

```bash
$ gem install cocoapods
```

To integrate PusherSwift into your Xcode project using CocoaPods, specify it in your `Podfile`:

```ruby
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '8.0'
use_frameworks!

pod 'PusherSwift'
```

Then, run the following command:

```bash
$ pod install
```

## Configuration

There are a number of configuration parameters which can be set for the Pusher client. They are:

- `authEndpoint` - the URL that the library will make an authentication request to if attempting to subscribe to a private or presence channel and you have not provided a secret
- `secret` - your app's secret so that authentication requests do not need to be made to your authentication endpoint and instead subscriptions can be authenticated directly inside the library (this is mainly desgined to be used for development)
- `userDataFetcher` - if you are subscribing to an authenticated channel and wish to provide a function to return user data
- `attemptToReturnJSONObject` - whether or not you'd like the library to try and parse your data as JSON (or not, and just return a string)
- `encrypted` - whether or not you'd like to use encypted transport or not

All of these configuration options can be set when instantiating the Pusher object, for example:

```swift
let pusher = Pusher(
  key: "APP_KEY",
  options: [
    "authEndpoint": "http://localhost:9292/pusher/",
    "encrypted": true
  ]
)
```

## Connection

A Websocket connection is established by providing your API key to the constructor function:

```swift
let pusher = Pusher(key: "APP_KEY")
```

This returns a client object which can then be used to subscribe to channels.


## Subscribing

### Public channels

The default method for subscribing to a channel involves invoking the `subscribe` method of your client object:


```swift
let myChannel = pusher.subscribe('my-channel')
```

This returns PusherChannel object, which events can be bound to.

### Private channels

Private channels are created in exactly the same way as public channels, except that they reside in the 'private-' namespace. This means prefixing the channel name:

```swift
let myPrivateChannel = pusher.subscribe('private-my-channel')
```

### Presence channels

Presence channels are created in exactly the same way as private channels, except that they reside in the 'presence-' namespace.

```swift
let myPresenceChannel = pusher.subscribe('presence-my-channel')
```

Note that both private and presence channels require the user to be authenticated in order to subscribe to the channel. This authentication can either happen inside the library, if you configured your Pusher object with your app's secret, or an authentication request is made to an authentication endpoint that you provide, again when instantiaing your Pusher object.

## Binding to events

Events can be bound to at 2 levels; globally and per channel. When binding to an event you can choose to save the return value, which is a unique identifier for the event handler that gets created. The only reason to save this is if you're going to want to unbind from the event at a later point in time. There is an example of this below.

### Global events

You can attach behaviour to these events regardless of the channel the event is broadcast to. The following is an example of an app that binds to new comments from any channel:

```swift
let pusher = Pusher(key: "MY_KEY")
pusher.subscribe("my-channel")

pusher.bind("new-comment", callback: { (data: AnyObject?) -> Void in
    if let data = data as? Dictionary<String, AnyObject> {
        if let commenter = data["commenter"] as? String, message = data["message"] as? String {
            println("\(commenter) wrote \(message)")
        }
    }
})
```

### Per-channel events

These are bound to a specific channel, and mean that you can reuse event names in different parts of your client application. The following might be an example of a stock tracking app where several channels are opened for different companies:

```swift
let pusher = Pusher(key: "MY_KEY")
let myChannel = pusher.subscribe("my-channel")

myChannel.bind("new-price", callback: { (data: AnyObject?) -> Void in
    if let data = data as? Dictionary<String, AnyObject> {
        if let price = data["price"] as? String, company = data["company"] as? String {
            println("\(company) is now priced at \(price)")
        }
    }
})
```

### Unbind event handlers

You can remove previously-bound handlers from an object by using the `unbind` function. For example,

```swift
let pusher = Pusher(key: "MY_KEY")
let myChannel = pusher.subscribe("my-channel")

let eventHandlerId = myChannel.bind("new-price", callback: { (data: AnyObject?) -> Void in
  ...
})

myChannel.unbind(eventName: "new-price", callbackId: eventHandlerId)
```
You can unbind from events at both the global and per channel level. For both objects you also have the option of calling `unbindAll`, which, as you can guess, will unbind all eventHandlers on the object.


## Testing

There are a set of tests for the library that can be run using the standard methods (Command-U in Xcode). The tests also get run on [Travis-CI](https://travis-ci.org/pusher/pusher-websocket-swift).


## Communication

- If you **need help**, use [Stack Overflow](http://stackoverflow.com/questions/tagged/pusherswift). (Tag 'pusherswift').
- If you still **need help**, visit [Pusher support](https://support.pusher.com).
- If you'd like to **ask a general question**, use [Stack Overflow](http://stackoverflow.com/questions/tagged/pusherswift).
- If you **found a bug**, open an issue.
- If you **have a feature request**, open an issue.
- If you **want to contribute**, submit a pull request (preferrably with some tests :) ).


## Credits

PusherSwift is owned and maintained by the [Pusher](https://pusher.com). It was origianlly cretead by [Hamilton Chapman](https://github.com/hamchapman)

## License

PusherSwift is released under the MIT license. See [LICENSE](https://github.com/pusher/pusher-websocket-swift/blob/master/LICENSE) for details.