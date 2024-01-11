# Neovim 

## Installation

```
brew install neovim
```

## Groking the setup 
https://www.youtube.com/watch?v=w7i4amO_zaE

ThePrimeagen did a fantastic tutorial on getting everything setup in a productive way.

## My Settings

https://github.com/dawguy/dotfiles/tree/master/nvim

Honestly I don't quite understand how it knows how to order the packages.


This is probably my favorite. I don't particularly like losing imports on save since I have a habit of :w all the time. This in combination of a .zshrc alias to auto run go fmt for all changes I've made using `git diff main..HEAD` It's pretty easy to always have formatted code.

```lua
-- Go fmt easily
vim.keymap.set("n", "<leader>gf", "<cmd>!go fmt %<CR>", { silent = true })
```
