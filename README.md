# remix-electron-demo

An demo of how to use Electron with Remix

![demo screenshot](./screenshot.png)

## How

- Create a Node.js server which serves a Remix app
- Create an Electron window, load the server URL
- That's it ¯\\\_(ツ)\_/¯

## Using Electron APIs

Importing `"electron"` directly in route files results in Electron trying to get bundled and called in the renderer process.

To circumvent this, I created an `electron.server.js` file, which re-exports from `electron`. The `.server` suffix tells Remix to only load it in the main process. You should use `.server` for any code that runs in the main process and uses node/electron APIs.

Likewise, for any code running in the renderer process, e.g. using the [clipboard](https://www.electronjs.org/docs/latest/api/clipboard) module, you can use the `.client` suffix. Using electron modules will require enabling `nodeIntegration` on the `BrowserWindow`.

## Motivation

Electron has [a comprehensive list of security recommendations](https://www.electronjs.org/docs/latest/tutorial/security) to follow when building an app, especially if that app interacts with the web. Which includes, but is not limited to:

- Using `preload.js` files to expose specific electron functionality to your app, via globals
- Using IPC communication
- Avoiding `remote.require` (which has since been removed)

These practices can lead to a lot of awkward boilerplate and splitting up related code across multiple files and domains.

With this project and structure, you can freely use Electron APIs in Remix loader functions. It's a Node process with full Node capabilities, with access to the full Electron API, none of which runs in the browser.

The browser only receives data and renders a view. Additionally, you can neatly colocate your main process code right beside the related renderer code in a route file.

Thinking about it another way: it's like a normal Remix web app, except Electron is your backend.
