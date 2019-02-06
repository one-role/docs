# Setting up the Visual Studio Code editor

The *Visual Studio Code* editor (`vscode`) is an Open Source and free
editor, made by Microsoft and a lot of contributors.

## Download and install Visual Code Editor

* [Download link](https://code.visualstudio.com)

## Install the following extensions

* haaaad.ansible
* wholroyd.jinja

## Install some optional extensions

Examples:

* Markdown Theme Kit
* Alignment
* Annotator
* Git History
* Markdown linter

You can install these extension through the editor or from the
commandline.

```none
code --install-extension vscoss.vscode-ansible
code --install-extension dhoeric.ansible-vault
code --install-extension wholroyd.jinja
code --install-extension annsk.alignment
code --install-extension dkundel.vscode-new-file
code --install-extension donjayamanne.githistory
code --install-extension felipecaputo.git-project-manager
code --install-extension ms-vscode.Theme-MarkdownKit
code --install-extension ryu1kn.annotator
code --install-extension stkb.rewrap
```

The *vscode* editor can be tweaked to the bone. You can find some
settings and keyboard shortcuts below.

### Settings

```none
// Place your settings in this file to override the default settings
{
  "editor.fontFamily": "'Droid Sans Mono', 'Courier New', monospace, 'Droid Sans Fallback'",
  "editor.fontSize": 14,
  "editor.rulers": [72, 80, 124, 132],
  "editor.cursorStyle": "block",
  "editor.cursorBlinking": "blink",
  "editor.detectIndentation": false,
  "editor.insertSpaces": false,
  "editor.fontLigatures": true,
  "editor.renderIndentGuides": true,
  "editor.renderWhitespace": "boundary",

  // Zoomlevel for all windows
  "window.zoomLevel": 1,

  // When enabled, will show the Welcome experience on startup.
  "workbench.welcome.enabled": false,
  "workbench.colorTheme": "Midnight",
  "workbench.iconTheme": "material-icon-theme",

  // Configure glob patterns for excluding files and folders.
  "files.exclude": {
    "**/.git": true,
    "**/.svn": true,
    "**/.hg": true,
    "**/*.pyc": true,
    "**/.DS_Store": true,
    "**/._.DS_Store": true,
    "**/.idea": true
  },

  // When enabled, will trim trailing whitespace when saving a file.
  "files.trimTrailingWhitespace": true,

  // Set file associations
  "files.associations": {
    "**/ansible/**/*.yml": "ansible",
    "**/infra/**/*.yml": "ansible"
  },

  // Settings for file types
  "[shellscript]": {
    "editor.tabSize": 4,
    "editor.insertSpaces": false
  },
  "[ansible]": {
    "editor.tabSize": 2,
    "editor.insertSpaces": true
  },
  "[markdown]": {
    "editor.wordWrap": "wordWrapColumn",
    "editor.wrappingIndent": "same",
    "editor.wordWrapColumn": 72
  },

  // Integrated terminal settings
  "terminal.integrated.shell.linux": "/bin/zsh",
  "terminal.integrated.shellArgs.linux": [ "-l" ],
  "terminal.integrated.shell.osx": "/bin/zsh",
  "terminal.integrated.shellArgs.osx": [ "-l" ],
  //
  "terminal.integrated.fontSize": 15,
  "terminal.integrated.fontLigatures": true,
  "terminal.integrated.cursorBlinking": true,
  "terminal.integrated.scrollback": 100000,

  "terminal.integrated.commandsToSkipShell": [
    "editor.action.toggleTabFocusMode",
    "workbench.action.debug.continue",
    "workbench.action.debug.restart",
    "workbench.action.debug.run",
    "workbench.action.debug.start",
    "workbench.action.debug.stop",
    "workbench.action.openNextRecentlyUsedEditorInGroup",
    "workbench.action.openPreviousRecentlyUsedEditorInGroup",
    "workbench.action.quickOpen",
    "workbench.action.showCommands",
    "workbench.action.terminal.clear",
    "workbench.action.terminal.copySelection",
    "workbench.action.terminal.focus",
    "workbench.action.terminal.focusNext",
    "workbench.action.terminal.focusPrevious",
    "workbench.action.terminal.kill",
    "workbench.action.terminal.new",
    "workbench.action.terminal.paste",
    "workbench.action.terminal.runSelectedText",
    "workbench.action.terminal.scrollDown",
    "workbench.action.terminal.scrollDownPage",
    "workbench.action.terminal.scrollToBottom",
    "workbench.action.terminal.scrollToTop",
    "workbench.action.terminal.scrollUp",
    "workbench.action.terminal.scrollUpPage",
    "workbench.action.terminal.toggleTerminal",
    "workbench.files.action.focusFilesExplorer",
    "workbench.files.action.focusOpenEditorsView",
    "workbench.action.focusFirstEditorGroup"
  ],

  // Settings for new files
  "newFile.defaultBaseFileName": "newFile",
  "newFile.relativeTo": "file", // "file", "root" or "project"
  "newFile.defaultFileExtension": ".pp",
  "newFile.rootDirectory": "~",
  "newFile.showFullPath": false,

  // Yes!! minimap
  "editor.minimap.enabled": true

  // No bloody way!!
  "telemetry.enableCrashReporter": false,
  "telemetry.enableTelemetry": false
}
```

### Keyboard shortcuts

```none
// Place your key bindings in this file to overwrite the defaults
[
  { "key": "alt+t",                   "command": "workbench.action.terminal.new",
                                         "when": "terminalFocus" },
  { "key": "alt+left",                "command": "workbench.action.terminal.focusPrevious",
                                         "when": "terminalFocus" },
  { "key": "alt+right",               "command": "workbench.action.terminal.focusNext",
                                         "when": "terminalFocus" },
  { "key": "alt+left",                "command": "workbench.action.previousEditor",
                                         "when": "!terminalFocus" },
  { "key": "alt+right",               "command": "workbench.action.nextEditor",
                                         "when": "!terminalFocus" },
  { "key": "shift+insert",            "command": "workbench.action.terminal.paste",
                                         "when": "terminalFocus" },

  { "key": "alt+f1",                  "command": "workbench.files.action.focusFilesExplorer" },
  { "key": "alt+f2",                  "command": "workbench.files.action.focusOpenEditorsView" },

  { "key": "ctrl+l",                  "command": "expandLineSelection",
                                         "when": "editorTextFocus" },

  { "key": "ctrl+n",                  "command": "extension.createNewFile" },

  // Alignment
  { "key": "alt+a",                   "command": "alignment.align",
                                         "when": "editorHasSelection" },

  { "key": "ctrl+shift+alt+down",     "command": "cursorColumnSelectDown",
                                         "when": "editorTextFocus" },
  { "key": "ctrl+shift+alt+left",     "command": "cursorColumnSelectLeft",
                                         "when": "editorTextFocus" },
  { "key": "ctrl+shift+alt+pagedown", "command": "cursorColumnSelectPageDown",
                                         "when": "editorTextFocus" },
  { "key": "ctrl+shift+alt+pageup",   "command": "cursorColumnSelectPageUp",
                                         "when": "editorTextFocus" },
  { "key": "ctrl+shift+alt+right",    "command": "cursorColumnSelectRight",
                                         "when": "editorTextFocus" },
  { "key": "ctrl+shift+alt+up",       "command": "cursorColumnSelectUp",
                                         "when": "editorTextFocus" },
  { "key": "ctrl+J",                   "command": "editor.action.joinLines",
                                         "when": "editorTextFocus" }
]
```
