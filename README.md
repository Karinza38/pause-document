# A pauseFrame API Proposal

There are times for frames to be busy, and times when it's wasteful. When it's wasteful, pause it, so that it's not bothering your user. You, or the user, can easily resume it later.

```javascript
var frameElement = document.getElementById("hungryFrameId");
frameElement.pauseFrame(true /* show play button ux */);
```

And when you're ready to resume the frame:
```javascript
frameElement.resumeFrame();  // does nothing if frame isn't paused
```

# Use Cases
* Pausing frames that violate policies (e.g., [TransferSizePolicy](https://github.com/WICG/transfer-size). This provides a gentle, yet firm response to misbehaving frames.
* Pausing resource-intensive frames that the user isn't currently paying attention to. Why run a game's tight graphics loop while the user is busy reading the instructions in a different frame?
* As a gentle, yet firm mechanism for browsers to automatically intervene on resource-intensive frames.


# A bit more about what pausing does
When a frame is paused, its event loops don't run. This means any scheduled tasks (such as timers) won't fire, they'll just sit in the queue, ready to run when the frame resumes. In addition to blocking scheduled tasks, the browser will also queue and defer resource requests generated by the frame, to be started upon resuming.

When a frame is paused, if the ux argument is true, the browser will make it visually obvious to the user. Imagine the frame turning black with a big play button on it. When the user presses the play button, the frame is resumed. If the ux argument is false, no visual change will be made apparent to the user. The frame will just be static and only resumable via script.

