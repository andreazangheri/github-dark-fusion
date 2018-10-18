# Deprecated! 🎉

There's now a dark theme (currently in beta) built into GitHub Desktop.

Access it from **File > Options... > Appearance**

--------------------------------------------------------------------------------

# GitHub Desktop Dark Theme

A sleek stopgap stylesheet for adding dark mode to GitHub Desktop ([issue #1515](https://github.com/desktop/desktop/issues/1515))

## Install

1. Open the devtools with **View > Toggle developer tools** (or <kbd>Ctrl+Shift+I</kbd>)
2. Paste in the following into the Console and hit <kbd>Enter</kbd>:
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

const link_to_inject = '<link href="https://rawgit.com/1j01/d8f8d11785d8fb21a70654e7aa8a4553/raw/desktop-dark.css" rel="stylesheet">';
html = html
	.replace(/<link[^>]+(rawgit|custom-theme)[^>]+>/, '') // remove existing custom stylesheet if there is one
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
Try opening the devtools console and hitting <kbd>↑</kbd> + <kbd>Enter</kbd>.
(If that doesn't work you can come back here and see if the code is updated, or if it's deprecated because they've finally added a dark mode.)

## TODO

- pull in the rest of the changes by @ObserverOfTime
	- what is (or was) `#configure-git-user .commit-list-example`? (and does it need styling?)
- `.pull-request-tab .count` (the `background` is hardcoded, but uses CSS variable for text color, tisk tisk)
- fix missing border at top of branches dropdown (`.branches-container > .tab-bar { border-top: var(--base-border); }`)

## Thanks

Thanks to @ObserverOfTime for improved cross platform compatibility in the install script and higher contrast colors for the code highlighting in diffs