# desktopCapturer

The `desktopCapturer` module can be used to get available sources that can be
used to be captured with `getUserMedia`.

```javascript
// In the renderer process.
var desktopCapturer = require('electron').desktopCapturer;

desktopCapturer.getSources({types: ['window', 'screen']}, function(error, sources) {
  if (error) throw error;
  for (var i = 0; i < sources.length; ++i) {
    if (sources[i].name == "Electron") {
      navigator.webkitGetUserMedia({
        audio: false,
        video: {
          mandatory: {
            chromeMediaSource: 'desktop',
            chromeMediaSourceId: sources[i].id,
            minWidth: 1280,
            maxWidth: 1280,
            minHeight: 720,
            maxHeight: 720
          }
        }
      }, gotStream, getUserMediaError);
      return;
    }
  }
});

function gotStream(stream) {
  document.querySelector('video').src = URL.createObjectURL(stream);
}

function getUserMediaError(e) {
  console.log('getUserMediaError');
}
```

## Methods

The `desktopCapturer` module has the following methods:

### `desktopCapturer.getSources(options, callback)`

* `options` Object
  * `types` Array - An array of String that lists the types of desktop sources
    to be captured, available types are `screen` and `window`.
  * `thumbnailSize` Object (optional) - The suggested size that thumbnail should
    be scaled, it is `{width: 150, height: 150}` by default.
* `callback` Function

Starts a request to get all desktop sources, `callback` will be called with
`callback(error, sources)` when the request is completed.

The `sources` is an array of `Source` objects, each `Source` represents a
captured screen or individual window, and has following properties:
* `id` String - The id of the captured window or screen used in
  `navigator.webkitGetUserMedia`. The format looks like `window:XX` or
  `screen:XX` where `XX` is a random generated number.
* `name` String - The described name of the capturing screen or window. If the
  source is a screen, the name will be `Entire Screen` or `Screen <index>`; if
  it is a window, the name will be the window's title.
* `thumbnail` [NativeImage](NativeImage.md) - A thumbnail image.

**Note:** There is no guarantee that the size of `source.thumbnail` is always
the same as the `thumnbailSize` in `options`. It also depends on the scale of
the screen or window.
