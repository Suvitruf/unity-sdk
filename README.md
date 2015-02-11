# Infinario Unity SDK

Infinario Android SDK is available in this Git repository: <a href="https://github.com/infinario/unity-sdk">https://github.com/infinario/unity-sdk</a>. It provides tracking capabilities for your application.

## Installation

* Download or clone this repository from your command line: ```git clone https://github.com/Infinario/unity-sdk.git```
* Copy the contents of ```source/Assets/Scripts``` directory to your Unity project's ```Scripts/``` directory.

## Usage

### Basic Tracking

To start tracking, you need to know your ```company_token```. To initialize the tracking, simply create an instance of the ```Infinario``` class:

```
var infinario = new Infinario.Infinario(<your_company_token>);
```

Now you can track events by calling the ```Track``` method:
```
infinario.Track("my_user_action");
```
What happens now, is that an event called ```my_user_action``` is recorded for the current player.

### Identifying Players
To control the identity of the current player use the ```Identify``` method. By calling
```
infinario.Identify("player@example.com");
```

you can register a new player in Infinario. All events you track by the ```Track``` method from now on will belong to this player. To switch to an existing player, simply call ```Identify``` with his name. You can switch the identity of the current player as many times as you need to.

### Anonymous Players
Up until you call ```Identify``` for the first time, all tracked events belong to an anonymous player (internally identified with a cookie). Once you call ```Identify```, the previously anonymous player is automatically merged with the newly identified player.

### Adding Properties
Both ```Identify``` and ```Track``` accept an optional parameter that can be used to add custom information (properties) to the respective entity. Usage is straightforward:
```
infinario.Track("my_player_action", new Dictionary<string,object> {
                                                          {"hp_delta", -10}
                                                        });                                       

infinario.Identify("player@example.com", new Dictionary<string,object> {
                                                          {"first_name", "John"},
                                                          { "last_name", "Doe" }
                                                        }); 
infinario.Update(new Dictionary<string,object> {{"level", 1}}); // Adds properties to the current customer
```
### Timestamps
The SDK automatically adds timestamps to all events. To override this behavior, use one of the following overriden methods:
```
infinario.Track("my_player_action", Infinario.Command.Epoch());
infinario.Track("my_player_action", <properties> , Infinario.Command.Epoch());		
```

###Player Sessions
Infinario automatically manages player sessions. Each session starts with a ```session_start``` event and ends with ```session_end```. Each of these events denotes the boundaries of a single player's session ended by either session timeout (currently 20 minutes of inactivity) or by player logout caused by calling ```Identify``` on a different player.

Once started, the SDK tries to recreate the previous session from its persistent cache. If it fails to, or the session has already expired it automatically creates a new one.

###Offline Behavior

Once instantized, the SDK collects and sends all tracked events continuously to the Infinario servers. 

However, if your application goes offline, the SDK guarantees you to re-send the events once online again (up to a approximately 5k offline events). This synchronization is transparent to you and happens in the background.

###To run smoothly...
- Make sure you create at most one instance of ```Infinario``` during your application lifetime.
- If you wish to override some of the capabilities (e.g. session management), please note that we will not be able to give any guarantees.
