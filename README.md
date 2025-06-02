# shared experiences made easy

## Introduction
As headsets develop more powerful AR features, users want to be able to interact with other users in the same environment.
This requires a common coordinate system and this is difficult to achieve.

Developers would have to ask users to somehow agree on an origin by standing in a certain spot or touching a common point. These actions tend to be confusing for users, error prone and very imprecise.
WebXR offers image tracking on certain platforms but that is difficult to set up and also suffers from being error prone.

These drawbacks made it so shared immersive experiences are not commmon.

## Introducing shared spaces
The Quest browser added an experimental feature named "shared spaces".
This new feature gives the developer the ability to automatically create a shared coordinate space among headsets in the same room. These headsets will also share a unique ID which they can use to set up a group.
The feature is available as an experiment in v39 of the Quest browser. To enable it, go to chrome://flags, search for "WebXR experiments", enable it and restart the browser.

To use it, the developer adds the "shared" feature to the []`requestSession`](https://immersive-web.github.io/webxr/#dom-xrsystem-requestsession) call and once the session is running, they can ask for a "shared" reference space. This space has a unique ID and is shared among the headsets in the same area. With this information, the developer should be able to create a shared experience since coordinates between the headset can be freely interchanged.

## Properties of shared spaces
- Each shared space is bound to size of a room. Headsets that are further away will not participate but they may start to participate if they get closer.
- A shared space is only exposed to the particular site. For instance, 'bar.com/a.html' will not be able to see the shared space of 'bar.com/b.html'. They will each get a unique space and uuid.
- When the WebXR session starts, it may take a couple of seconds to establish the correct shared space. Until then, the browser will report a default shared space. After the correct one is established, the `reset` event will be called on the shared space and a new coordinate system and UUID will be established. If the headset was first to go immersive, no reset event is generated.
- Participants may enter and leave at will. They will always be  able to establish a common coordiante system when restarting the WebXR session. (By design, the origin of the first headset that created a shared space will be origin of the common coordinate space).
- When a partipant exists WebXR, the shared spade is lost and will need to be recreated when reentering WebXR.
- Headsets may come and go freely from the shared space, but once the last one leaves, the shared space is lost. It may be possible to recover it but we need more developer feedback on a good API shape for this.

## Additions to the WebXR spec
- Add `shared` to list of reference space types in https://immersive-web.github.io/webxr/#enumdef-xrreferencespacetype
- Create a new object `XRSharedReferencSpace` in https://immersive-web.github.io/webxr/#spaces. This object has a UUID string and is created with the usual [requestReferenceSpace](https://immersive-web.github.io/webxr/#dom-xrsession-requestreferencespace) API.

## Sample site
This git repo also contains a basic example of a simple game that uses a shared space. It's hosted [here](https://sharedshooter.arvr.social/).

Once a user goes immersive, they will see other joined users and will be able to "shoot" at them using their controllers. The game will keep track of the number of succesful hits and display it above each user.

Here's a small recording of me and my daughter playing the game:
[![shared space demo](https://img.youtube.com/vi/Eap8upxWEcw/maxresdefault.jpg)](https://www.youtube.com/shorts/Eap8upxWEcw)

Quick facts:
- the experience uses [peerjs](https://peerjs.com/) to communicate between headsets.
- the experience will send the shared space UUID and the peerjs UUID to a server and it will return a list of other headsets with that same shared space UUID. This list is then used to connect to the other sets.
- every 5 seconds, the location of the headset and the controllers are sent to the other participants.
- hit testing of the bullits is done on the headset that does the "shooting". Score is also kept on the server and sent down to all participants.



