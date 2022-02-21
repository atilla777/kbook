##### [Linux](index.md)
##### [Главная страница](../index.md)
## Кастомизация рабочего окружения Linux
#### Настройка псевданима для запуска postgresql в контейнере
Создать папку для постоянного хранения файлов базы (том docker)
mkdir -p ```bash
"$HOME/docker/volumes/postgres
```
Добавить в ./bashrc псевдоним (команда запуска docker с postgresql)
```
 alias postgres='docker run --rm --name postgres -e POSTGRES_PASSWORD=password -v="$HOME/docker/volumes/postgres:/var/lib/postg resql/data" -p 5432:5432 postgres:14'
```
запустить postgresql
```bash
postgres
```
#### Установка vi как редактора строки shell в текущем сеансе

```bash
set -o vi
```
То же самое, для всех сеансов — добавить set -o vi в файл

**~/.bashrc**
#### Установить vi редактором по умолчанию
```bash
sudo update-alternatives --config editor
```
#### Отображение в строке shell текущей ветки git
В **~/.bashrc**
```bash
parse_git_branch() {
  git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ (\1)/'
}
PS1="\u@\h \[\033[32m\]\w\[\033[33m\]\$(parse_git_branch)\[\033[00m\] $ "
```
#### Vundle плагин для Vim
Установить [Vundle](https://github.com/VundleVim/Vundle.vim) по инструкции сайта прокекта.

Открыть файл конфигурации **vim**:
```bash
vim ~/.vimrc
```
Вставить в стартовую конфигурацию vim:
```vi
set nocompatible              " be iMproved, required
syntax on
filetype off                  " required

" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
" alternatively, pass a path where Vundle should install plugins
"call vundle#begin('~/some/path/here')

" let Vundle manage Vundle, required
Plugin 'VundleVim/Vundle.vim'
Plugin 'bling/vim-airline'
Plugin 'jpo/vim-railscasts-theme'
Plugin 'ervandew/supertab'
Plugin 'scrooloose/nerdtree'
Plugin 'scrooloose/syntastic'
Plugin 'MarcWeber/vim-addon-mw-utils'
Plugin 'tomtom/tlib_vim'
Plugin 'garbas/vim-snipmate'
Plugin 'airblade/vim-gitgutter'
"Plugin 'tpope/vim-fugitive'
Plugin 'terryma/vim-multiple-cursors'
Plugin 'tpope/vim-rails'
Plugin 'slim-template/vim-slim.git'
Plugin 'tpope/vim-endwise'
Plugin 'ngmy/vim-rubocop'
Plugin 'rhysd/vim-crystal'
Plugin 'fatih/vim-go'
"Plugin 'kien/ctrlp.vim'
" All of your Plugins must be added before the following line
call vundle#end()            " required
syntax enable
filetype plugin indent on    " required
" To ignore plugin indent changes, instead use:
"filetype plugin on
"
" Brief help
" :PluginList       - lists configured plugins
" :PluginInstall    - installs plugins; append `!` to update or just :PluginUpdate
" :PluginSearch foo - searches for foo; append `!` to refresh local cache
" :PluginClean      - confirms removal of unused plugins; append `!` to auto-approve removal
"
" see :h vundle for more details or wiki for FAQ
" Put your non-Plugin stuff after this line
color railscasts
set guifont=DejaVu\ Sans\ Mono\ 18
set nu
set hidden
set tabstop=2 shiftwidth=2 expandtab
"set listchars=tab:>-,trail:.
:set listchars=tab:\|\ ,trail:-,extends:>,precedes:<,nbsp:+
:highlight SpecialKey guifg=#333333 guibg=#111111
set list
set autochdir
set nobackup
set guitablabel=%N:%M%t
set laststatus=2
:set lines=35 columns=130
autocmd BufNewFile,BufRead *.slim setlocal filetype=slim
let NERDTreeShowHidden=1
set showtabline=2 " always show tabs in gvim, but not vim
" set up tab labels with tab number, buffer name, number of windows
fu! MyTabLabel(n)
let buflist = tabpagebuflist(a:n)
let winnr = tabpagewinnr(a:n)
let string = fnamemodify(bufname(buflist[winnr - 1]), ':t')
return empty(string) ? '[unnamed]' : string
endfu

fu! MyTabLine()
let s = ''
for i in range(tabpagenr('$'))
" select the highlighting
    if i + 1 == tabpagenr()
    let s .= '%#TabLineSel#'
    else
    let s .= '%#TabLine#'
    endif

    " set the tab page number (for mouse clicks)
    "let s .= '%' . (i + 1) . 'T'
    " display tabnumber (for use with <count>gt, etc)
    let s .= ' '. (i+1) . ' ' 

    " the label is made by MyTabLabel()
    let s .= ' %{MyTabLabel(' . (i + 1) . ')} '

    if i+1 < tabpagenr('$')
        let s .= ' |'
    endif
endfor
return s
endfu
set tabline=%!MyTabLine()
```
