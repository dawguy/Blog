Making a small note here: https://book.babashka.org/#project-setup
Auto-complete for shell babashka can be a thing!

```zsh
_bb_tasks() {
    local matches=(`bb tasks |tail -n +3 |cut -f1 -d ' '`)
    compadd -a matches
    _files # autocomplete filenames as well
}
compdef _bb_tasks bb
```
