# Hazel for Tauri

> This is a fork from [lemarier/tauri-update-server](https://github.com/lemarier/tauri-update-server) and [vercel/hazel](https://github.com/vercel/hazel).

[![CircleCI](https://circleci.com/gh/vercel/hazel/tree/master.svg?style=svg)](https://circleci.com/gh/vercel/hazel/tree/master)
[![XO code style](https://img.shields.io/badge/code_style-XO-5ed9c7.svg)](https://github.com/sindresorhus/xo)

This project lets you deploy an update server for [Electron](https://www.electronjs.org) apps with ease: You only need to click a button.

The result will be faster and more lightweight than any other solution out there! :rocket:

- Recommended by Electron [here](https://www.electronjs.org/docs/tutorial/updates#deploying-an-update-server)
- Built on top of [micro](https://github.com/zeit/micro), the tiniest HTTP framework for Node.js
- Pulls the latest release data from [GitHub Releases](https://help.github.com/articles/creating-releases/) and caches it in memory
- Refreshes the cache every **15 minutes** (custom interval [possible](#options))
- When asked for an update, it returns the link to the GitHub asset directly (saves bandwidth)
- Supports **macOS** and **Windows** apps
- Scales infinitely on [Vercel](https://vercel.com) Serverless Functions

## Usage

Open this link in a new tab to deploy Hazel on [Vercel](https://vercel.com):

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2FHuakunShen%2Ftauri-update-server&env=ACCOUNT%2CREPOSITORY&envDescription=Enter%20your%20GitHub%20user%2Forg%20slug%20and%20the%20name%20of%20the%20repository%20that%20contains%20your%25Tauri%20app.&envLink=https%3A%2F%2Fgithub.com%2Fvercel%2Fhazel#usage&repo-name=tauri-update-server)

Once deployed, Tauri app will automatically work given that the configuration file is set properly. See [Updater Docs](https://tauri.app/v1/guides/distribution/updater/).

```json
"updater": {
    "active": true,
    "endpoints": [
        "https://releases.myapp.com/update{{target}}/{{current_version}}"
    ],
    "dialog": true,
    "pubkey": "YOUR_UPDATER_SIGNATURE_PUBKEY_HERE"
}
```


That's it! :white_check_mark:

## Options

The following environment variables can be used optionally:

- `INTERVAL`: Refreshes the cache every x minutes ([restrictions](https://developer.github.com/changes/2012-10-14-rate-limit-changes/)) (defaults to 15 minutes)
- `PRE`: When defined with a value of `1`, only pre-releases will be cached
- `TOKEN`: Your GitHub token (for private repos)
- `URL`: The server's URL (for private repos - when running on [Vercel](https://vercel.com), this field is filled with the URL of the deployment automatically)

## Statistics

Since Hazel routes all the traffic for downloading the actual application files to [GitHub Releases](https://help.github.com/articles/creating-releases/), you can use their API to determine the download count for a certain release.

As an example, check out the [latest Hyper release](https://api.github.com/repos/vercel/hyper/releases/latest) and search for `mac.zip`. You'll find a release containing a sub property named `download_count` with the amount of downloads as its value.

## Routes

### /

Displays an overview page showing the cached repository with the different available platforms and file sizes. Links to the repo, releases, specific cached version and direct downloads for each platform are present.

### /download

Automatically detects the platform/OS of the visitor by parsing the user agent and then downloads the appropriate copy of your application.

If the latest version of the application wasn't yet pulled from [GitHub Releases](https://help.github.com/articles/creating-releases/), it will return a message and the status code `404`. The same happens if the latest release doesn't contain a file for the detected platform.

### /download/:platform

Accepts a platform (like "darwin" or "win32") to download the appropriate copy your app for.

If the cache isn't filled yet or doesn't contain a download link for the specified platform, it will respond like `/`.

### /update/:platform/:version

Checks if there is an update available by reading from the cache.

If the latest version of the application wasn't yet pulled from [GitHub Releases](https://help.github.com/articles/creating-releases/), it will return the `204` status code. The same happens if the latest release doesn't contain a file for the specified platform.

### /update/win32/:version/RELEASES

This endpoint was specifically crafted for the Windows platform (called "win32" [in Node.js](https://nodejs.org/api/process.html#process_process_platform)).

Since the [Windows version](https://github.com/Squirrel/Squirrel.Windows) of Squirrel (the software that powers auto updates inside [Electron](https://www.electronjs.org)) requires access to a file named "RELEASES" when checking for updates, this endpoint will respond with a cached version of the file that contains a download link to a `.nupkg` file (the application update).

## Credits

Huge thanks to my ([@leo](https://github.com/leo)'s) friend [Andy](http://twitter.com/andybitz_), who suggested the name "Hazel" (since the auto updater software inside [Electron](https://www.electronjs.org) is called "Squirrel") and [Matheus](https://twitter.com/matheusfrndes) for collecting ideas with me.

## Author

Leo Lamprecht ([@notquiteleo](https://twitter.com/notquiteleo)) - [Vercel](https://vercel.com)
