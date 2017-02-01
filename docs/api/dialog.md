# dialog

> Display native system dialogs for opening and saving files, alerting, etc.

Process: [Main](../glossary.md#main-process)

An example of showing a dialog to select multiple files and directories:

```javascript
const {dialog} = require('electron')
console.log(dialog.showOpenDialog({properties: ['openFile', 'openDirectory', 'multiSelections']}))
```

The Dialog is opened from Electron's main thread. If you want to use the dialog
object from a renderer process, remember to access it using the remote:

```javascript
const {dialog} = require('electron').remote
console.log(dialog)
```

## Methods

The `dialog` module has the following methods:

### `dialog.showOpenDialog([browserWindow, ]options[, callback])`

* `browserWindow` BrowserWindow (optional)
* `options` Object
  * `title` String (optional)
  * `defaultPath` String (optional)
  * `buttonLabel` String (optional) - Custom label for the confirmation button, when
    left empty the default label will be used.
  * `filters` [FileFilter[]](structures/file-filter.md) (optional)
  * `properties` String[] (optional) - Contains which features the dialog should use, can
    contain `openFile`, `openDirectory`, `multiSelections`, `createDirectory`
    and `showHiddenFiles`.
  * `normalizeAccessKeys` Boolean (optional) - Normalize the keyboard access keys
    across platforms. Default is `false`. Enabling this assumes `&` is used in
    the button labels for the placement of the keyboard shortcut access key
    and labels will be converted so they work correctly on each platform, `&`
    characters are removed on macOS, converted to `_` on Linux, and left
    untouched on Windows. For example, a button label of `Vie&w` will be
    converted to `Vie_w` on Linux and `View` on macOS and can be selected
    via `Alt-W` on Windows and Linux.
    * `message` (optional) _macOS_
* `callback` Function (optional)
  * `filePaths` String[] - An array of file paths chosen by the user

Returns `String[]`, an array of file paths chosen by the user,
if the callback is provided it returns `undefined`.

The `browserWindow` argument allows the dialog to attach itself to a parent window, making it modal.

The `filters` specifies an array of file types that can be displayed or
selected when you want to limit the user to a specific type. For example:

```javascript
{
  filters: [
    {name: 'Images', extensions: ['jpg', 'png', 'gif']},
    {name: 'Movies', extensions: ['mkv', 'avi', 'mp4']},
    {name: 'Custom File Type', extensions: ['as']},
    {name: 'All Files', extensions: ['*']}
  ]
}
```

The `extensions` array should contain extensions without wildcards or dots (e.g.
`'png'` is good but `'.png'` and `'*.png'` are bad). To show all files, use the
`'*'` wildcard (no other wildcard is supported).

If a `callback` is passed, the API call will be asynchronous and the result
will be passed via `callback(filenames)`

**Note:** On Windows and Linux an open dialog can not be both a file selector
and a directory selector, so if you set `properties` to
`['openFile', 'openDirectory']` on these platforms, a directory selector will be
shown.

### `dialog.showSaveDialog([browserWindow, ]options[, callback])`

* `browserWindow` BrowserWindow (optional)
* `options` Object
  * `title` String (optional)
  * `defaultPath` String (optional)
  * `buttonLabel` String (optional) - Custom label for the confirmation button, when
    left empty the default label will be used.
  * `filters` [FileFilter[]](structures/file-filter.md) (optional)
  * `message` (optional) _macOS_
  * `nameFieldLabel` String (optional) _macOS_ - Custom label for the string displayed
    in front of the filename text field.
  * `showsTagField` Boolean (optional) _macOS_
* `callback` Function (optional)
  * `filename` String

Returns `String`, the path of the file chosen by the user,
if a callback is provided it returns `undefined`.

The `browserWindow` argument allows the dialog to attach itself to a parent window, making it modal.

The `filters` specifies an array of file types that can be displayed, see
`dialog.showOpenDialog` for an example.

If a `callback` is passed, the API call will be asynchronous and the result
will be passed via `callback(filename)`

### `dialog.showMessageBox([browserWindow, ]options[, callback])`

* `browserWindow` BrowserWindow (optional)
* `options` Object
  * `type` String (optional) - Can be `"none"`, `"info"`, `"error"`, `"question"` or
  `"warning"`. On Windows, "question" displays the same icon as "info", unless
  you set an icon using the "icon" option.
  * `buttons` String[] (optional) - Array of texts for buttons. On Windows, an empty array
    will result in one button labeled "OK".
  * `defaultId` Integer (optional) - Index of the button in the buttons array which will
    be selected by default when the message box opens.
  * `title` String (optional) - Title of the message box, some platforms will not show it.
  * `message` String - Content of the message box.
  * `detail` String (optional) - Extra information of the message.
  * `icon` [NativeImage](native-image.md) (optional)
  * `cancelId` Integer (optional) - The value will be returned when user cancels the dialog
    instead of clicking the buttons of the dialog. By default it is the index
    of the buttons that have "cancel" or "no" as label, or 0 if there is no such
    buttons. On macOS and Windows the index of the "Cancel" button will always
    be used as `cancelId` even if it is specified.
  * `noLink` Boolean (optional) - On Windows Electron will try to figure out which one of
    the `buttons` are common buttons (like "Cancel" or "Yes"), and show the
    others as command links in the dialog. This can make the dialog appear in
    the style of modern Windows apps. If you don't like this behavior, you can
    set `noLink` to `true`.
* `callback` Function (optional)
  * `response` Number - The index of the button that was clicked

Returns `Integer`, the index of the clicked button, if a callback is provided
it returns undefined.

Shows a message box, it will block the process until the message box is closed.
It returns the index of the clicked button.

The `browserWindow` argument allows the dialog to attach itself to a parent window, making it modal.

If a `callback` is passed, the API call will be asynchronous and the result
will be passed via `callback(response)`.

### `dialog.showErrorBox(title, content)`

* `title` String - The title to display in the error box
* `content` String - The text content to display in the error box

Displays a modal dialog that shows an error message.

This API can be called safely before the `ready` event the `app` module emits,
it is usually used to report errors in early stage of startup.  If called
before the app `ready`event on Linux, the message will be emitted to stderr,
and no GUI dialog will appear.

## Sheets

On macOS, dialogs are presented as sheets attached to a window if you provide
a `BrowserWindow` reference in the `browserWindow` parameter, or modals if no
window is provided.

You can call `BrowserWindow.getCurrentWindow().setSheetOffset(offset)` to change
the offset from the window frame where sheets are attached.
