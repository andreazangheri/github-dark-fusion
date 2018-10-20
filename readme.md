# GitHub Desktop Dark Fusion Theme

![Theme](https://img.shields.io/badge/theme-dark--fusion-orange.svg)
![GitHub release](https://img.shields.io/github/release/typerror/github-dark-fusion.svg)
![Github commits (since latest release)](https://img.shields.io/github/commits-since/typerror/github-dark-fusion/latest.svg)
![Tested platform](https://img.shields.io/badge/macos-10.14-black.svg)
![GitHub issues](https://img.shields.io/github/issues/typerror/github-dark-fusion.svg)
![GitHub file size](https://img.shields.io/github/size/typerror/github-dark-fusion/desktop--dark-fusion.css.svg)
![GitHub](https://img.shields.io/github/license/typerror/github-dark-fusion.svg)

## Install

This theme will override your **currently selected** theme.

At the moment it has been tested on macOS High Sierra and Mojave 10.14

1. Open Github Desktop
2. Open the devtools with **View > Toggle developer tools** (or <kbd>Cmd+alt+I</kbd>)
3. Paste in the following into the **Console** and hit <kbd>Enter</kbd>:
```js
const fs = require('fs');
const path = require('path');
const res = process.resourcesPath;

let html_file_path;
if (fs.existsSync(path.join(res, 'app/index.html'))) {
	html_file_path = path.join(res, 'app/index.html');
} else if (fs.existsSync(path.join(res, 'app/dist/index.html'))) {
	html_file_path = path.join(res, 'app/dist/index.html');
} else {
	throw new Error(`Couldn't find index.html`);
}

let html = fs.readFileSync(html_file_path, "utf8");

const link_to_inject = '  <link href="https://cdn.jsdelivr.net/gh/typerror/github-dark-fusion@v0.5-alpha/desktop--dark-fusion.css" rel="stylesheet">';
html = html
	.replace(/<link[^>]+(cdn|custom-theme)[^>]+>/, '') // remove existing custom stylesheet if there is one
	.replace('</head>', `\n${link_to_inject}</head>`); // add stylesheet

try {
	fs.writeFileSync(html_file_path, html, 'utf8');
} catch(error) {
	if (html_file_path.startsWith('/tmp')) {
		console.error(`The theme cannot be applied to the AppImage since 'index.html' is saved in a temporary directory.`)
	} else if (error.code == 'EACCES') {
		console.error(`You need to have write access to '${html_file_path}' for the theme to be applied.`)
	}
	throw error;
}

// clear the cache so the stylesheet can update (could alternatively cache-bust with a URL parameter)
const {remote} = require('electron');
const win = remote.getCurrentWindow();
win.webContents.session.clearCache(() => {
	if (confirm('Ready to refresh? :)')) {
		location.reload();
	}
});
```

GitHub Desktop will refresh and the theme should be applied.

You'll need to reapply the stylesheet when GitHub Desktop updates.
