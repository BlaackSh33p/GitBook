# PlatformIO with Vim/Neovim

## 🚀 Using PlatformIO with Vim/Neovim: Complete Setup Guide

> This is a practical log of how to set up and integrate **PlatformIO** with **Vim/Neovim** — including project generation, language server (IntelliSense), building, uploading, and even serial monitoring (like Arduino IDE or Deviot in Sublime).

***

### 📦 1. Install PlatformIO Core

First, make sure you have PlatformIO Core (CLI) installed:

```bash
pip install platformio
```

Verify:

```bash
pio --version
```

***

### 📁 2. Create and Initialize a Project

Make a project folder and generate a PlatformIO project for Vim:

```bash
mkdir my_project && cd my_project
pio project init --ide vim --board <BOARD_ID>
```

👉 Find your board ID with:

```bash
pio boards
```

⚠️ If you later add libraries, you must reinitialize the project:

```bash
pio project init
```

***

### 🧰 3. Recommended Tools & Plugins

For Vim/Neovim you’ll want:

* **coc.nvim** → LSP client for IntelliSense
* **clangd** → C/C++ language server (autocompletion, diagnostics)
* **neomake** → async builds & linting
* **vim-pio** → PlatformIO command helpers

***

### 🔌 4. Installing Plugins (vim-plug)

First install [vim-plug](https://github.com/junegunn/vim-plug):

```bash
# For Vim
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim

# For Neovim
curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

Then in `~/.vimrc` (Vim) or `~/.config/nvim/init.vim` (Neovim):

```vim
call plug#begin('~/.vim/plugged')

Plug 'neoclide/coc.nvim', {'branch': 'release'}   " LSP client
Plug 'neomake/neomake'                            " Async builds
Plug 'nmoroze/vim-pio'                            " PlatformIO helpers

call plug#end()
```

Install plugins:

```vim
:PlugInstall
```

***

### ⚙️ 5. clangd Setup (LSP)

Install clangd:

```bash
# Ubuntu/Debian
sudo apt install clangd

# macOS
brew install llvm
```

Inside Vim/Neovim:

```vim
:CocInstall coc-clangd
```

Now you get autocompletion, jump-to-definition, error squiggles, etc.

***

### 🛠 6. Build & Upload Integration

Add these mappings in your `~/.vimrc` or `init.vim`:

```vim
" Build project with Ctrl+B
nnoremap <C-b> :make<CR>

" Upload project with Ctrl+U
nnoremap <C-u> :!pio run --target upload<CR>
```

Add this to auto-build on save:

```vim
" Auto-build with Neomake on save
let g:neomake_open_list = 2
autocmd BufWritePost *.c,*.cpp,*.h,*.hpp Neomake! pio
```

***

### 🖥 7. Serial Monitor Integration

PlatformIO has a built-in serial monitor:

#### Option 1: Run directly in Vim (works in both Vim & Neovim)

```vim
nnoremap <C-m> :!pio device monitor<CR>
```

#### Option 2: Open in a split terminal (Neovim only)

```vim
nnoremap <C-m> :split | terminal pio device monitor<CR>
```

#### Option 3: Add to Makefile

Add this target:

```make
monitor:
        pio device monitor
```

Then run:

```vim
:make monitor
```

⚡ You can also fix baud rate:

```vim
nnoremap <C-m> :!pio device monitor --baud 115200<CR>
```

or set `monitor_speed = 115200` in `platformio.ini`.

***

### 🎹 8. Suggested Workflow Mappings

Here’s a smooth Arduino-like experience inside Vim/Neovim:

```vim
" Build
nnoremap <C-b> :make<CR>

" Upload
nnoremap <C-u> :!pio run --target upload<CR>

" Serial Monitor
nnoremap <C-m> :!pio device monitor<CR>

" Build + Upload + Monitor (like "Run" in Arduino IDE)
nnoremap <C-r> :!pio run && pio run --target upload && pio device monitor<CR>
```

***

### ✅ 9. What You Get

* PlatformIO project generation inside Vim/Neovim
* IntelliSense, go-to-definition, error highlighting via **coc.nvim + clangd**
* Async builds with **Neomake**
* Keybindings for **build, upload, monitor**
* Arduino-like workflow, but in Vim/Neovim 💪

***

## 🔮 Final Notes

* For **Vim 8**: You must use `coc.nvim` since it works in both Vim and Neovim.
* For **Neovim (0.5+)**: You could instead use the built-in LSP client (`nvim-lspconfig` + clangd), but `coc.nvim` keeps things universal.
* Always re-run `pio project init` after adding new libraries.
