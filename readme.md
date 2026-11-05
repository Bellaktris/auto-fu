# auto-fu: inline auto-completion for zsh

As you type, auto-fu runs the completion system and shows the unambiguous part
of the match inline - highlighted, and not part of the buffer. Tab commits it
and starts cycling; any other key just types over it.

Fork of [auto-fu.zsh](https://github.com/hchbaw/auto-fu.zsh) by Takeshi Banse.

## Installation

Loaded by the prezto completion module, which also sets every `:auto-fu:*`
style:

    zstyle ':prezto:module:completion' autocompletion 'yes'   # .zpreztorc

See `modules/completion/init.zsh` for the `source .../auto-fu && auto-fu-install`
call and the styles it sets. After editing anything here, re-run
`~/.files/zsh/runcoms/.zlogin` to refresh the `.zwc` caches.

## Layout

Sourced in dependency order by `auto-fu`:

| file                 | what is in it                                              |
| -------------------- | ---------------------------------------------------------- |
| `auto-fu`            | entry point: documentation and the source loop             |
| `auto-fu-predicates` | "may we complete here?" - one call per keystroke, hot path |
| `auto-fu-highlight`  | `$region_highlight` bookkeeping, zsh-syntax-highlighting   |
| `auto-fu-complete`   | completion-system glue, and Tab (`afu+complete-word`)      |
| `auto-fu-core`       | the engine, state, keymap tracking, line init              |
| `auto-fu-widgets`    | the `afu+*` ZLE widgets                                    |
| `auto-fu-install`    | widget registration, keymaps, ZLE rebinding                |

The central invariant: the inline completion is never part of the real buffer.
`$buffer_cur` holds the line as typed and `$afu_in_p` is 1 while a completion
is displayed but not committed; anything that accepts, runs or hands off the
line restores `$buffer_cur` first. `modules/fzf/init.zsh` relies on both.

## Configuration

Styles, all under `:auto-fu:var` unless noted:

| style                            | meaning                                        |
| -------------------------------- | ---------------------------------------------- |
| `enable` / `disable`             | widgets that do (not) auto-complete; `all`     |
| `postdisplay`                    | text shown below the line while editing        |
| `postdisplay/clearp`             | clear it when the line is accepted (default on)|
| `resume`                         | widgets that switch auto-fu back on            |
| `autoablep-function`             | replace the whole "may we complete" decision   |
| `autoable-function/preds`        | replace the predicate list                     |
| `autoable-function/skipwords`    | glob patterns: words never completed           |
| `autoable-function/skiplbuffers` | ... same, matched against the line so far      |
| `autoable-function/skiplines`    | ... same, matched against the whole command    |
| `track-keymap-skip`              | keymaps whose transitions are not tracked      |
| `:auto-fu:highlight completion`  | style for the inline completion                |
| `:auto-fu:highlight completion/one` | ... when there is exactly one match         |

Parameters:

| parameter                  | default | meaning                             |
| -------------------------- | ------- | ----------------------------------- |
| `AUTO_FU_TIMEOUT`          | `0.1`   | seconds before a completion is interrupted |
| `AUTO_FU_MAX_DIR_ENTRIES`  | `200`   | directories larger than this wait for Tab  |
| `AUTO_FU_NOCP`             | unset   | set to allow completing after a space      |
| `AUTO_FU_DEBUG`            | unset   | file to log predicate vetoes to            |

Keys bound in the `afu` keymap (a copy of `main`): Tab and shift-Tab complete,
`^M` accepts, `^D` deletes or exits, arrow keys move without disturbing the
completion, and `Escape` leaves for `afu-vicmd` if `main` is a vi keymap.

## Credits

Takeshi Banse, `auto-fu.zsh`, two-clause BSD - see `license`.
