---
draft: true
title: IJKL Workflow
date: 2022-08-03T21:46:32.005Z
tags:
  - programming
  - workflow
summary: Using IJKL instead of the arrow keys to keep your hands on the home row
  and save time.
---
TODO: why?

TODO: general key bindings table

Writing this article makes me wonder if there's some additional ways to bind Alt + IJKL to arrow keys in other applications at the OS level...

### VSCode IJKL Keybindings
```json
[

// move cursor with ijklh;
{ "key": "alt+i",   "command":"cursorUp", "when": "editorTextFocus" },
{ "key": "alt+k",   "command":"cursorDown", "when": "editorTextFocus" },
{ "key": "alt+j",   "command":"cursorLeft", "when": "editorTextFocus" },
{ "key": "alt+l",   "command":"cursorRight", "when": "editorTextFocus" },
{ "key": "alt+h",   "command":"cursorHome", "when": "editorTextFocus" },
{ "key": "alt+;",   "command":"cursorEnd", "when": "editorTextFocus" },

// move and select
{ "key": "alt+shift+i",   "command":"cursorUpSelect", "when": "editorTextFocus" },
{ "key": "alt+shift+ctrl+i",   "command":"cursorUpSelect", "when": "editorTextFocus" },
{ "key": "alt+shift+k",   "command":"cursorDownSelect", "when": "editorTextFocus" },
{ "key": "alt+shift+ctrl+k",   "command":"cursorDownSelect", "when": "editorTextFocus" },
{ "key": "alt+shift+j",   "command":"cursorLeftSelect", "when": "editorTextFocus" },
{ "key": "alt+shift+l",   "command":"cursorRightSelect", "when": "editorTextFocus" },
{ "key": "alt+shift+h",   "command":"cursorHomeSelect", "when": "editorTextFocus" },
{ "key": "alt+shift+;",   "command":"cursorEndSelect", "when": "editorTextFocus" },

// move by word
{ "key": "alt+ctrl+j",   "command":"cursorWordStartLeft", "when": "editorTextFocus" },
{ "key": "alt+ctrl+l",   "command":"cursorWordEndRight", "when": "editorTextFocus" },
{ "key": "alt+ctrl+shift+j",   "command":"cursorWordStartLeftSelect", "when": "editorTextFocus" },
{ "key": "alt+ctrl+shift+l",   "command":"cursorWordEndRightSelect", "when": "editorTextFocus" },

// move line up / down
{ "key": "alt+ctrl+i",   "command":"editor.action.moveLinesUpAction", "when": "editorFocus && !editorReadonly" },
{ "key": "alt+ctrl+k",   "command":"editor.action.moveLinesDownAction", "when": "editorFocus && !editorReadonly" },

]
```