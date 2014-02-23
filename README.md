multi_clipboard
================

Clipboard manager for GNU screen.

# Installation

:white_check_mark: current version requires [sentaku](https://github.com/rcmdnk/sentaku).

If you use Homebrew or cURL to insatll, sentaku will be installed automatically, too.

Please install sentaku or use
[standalone version](https://github.com/rcmdnk/multi_clipboard/tree/standalone)
(standalone version could be obsolete).

## cURL

You can use an install script on the web like:

    $ curl -fsSL https://raw.github.com/rcmdnk/multi_clipboard/install/install.sh| sh

This will install scripts to `/usr/bin`
and you may be asked root password.

If you want to install other directory, do like:

    $ curl -fsSL https://raw.github.com/rcmdnk/multi_clipboard/install/install.sh|  prefix=~/usr/local/ sh

## Homebrew at OS X

On Mac, you can install scripts by [Homebrew](https://github.com/mxcl/homebrew):

    $ brew tap rcmdnk/rcmdnkpac
    $ brew install multi_clipboard

If you have [brewall](https://github.com/rcmdnk/homebrew-brewall), add following lines to Brewfile:

    tap 'rcmdnk/rcmdnkpac'
    brew 'multi_clipboard'

then, do:

    $ brewall install

Or if you write like:

    tapall 'rcmdnk/rcmdnkpac'

and do `brewall install`, you will have all useful scripts in
[rcmdnkpac](https://github.com/rcmdnk/homebrew-rcmdnkpac).

## Download

Or, simply download scripts and set where you like.

:warning: Install [sentaku](https://github.com/rcmdnk/sentaku), too.


# Setup

## Setup for GNU screen

Add following lines to `.screenrc`

    # Screen exchange file
    bufferfile "$SCREENEXCHANGE" # SCREENEXCHANGE must be set in .bashrc !!!

    # Overwrite keys in copy mode
    bindkey -m ' ' eval 'stuff \040' 'writebuf' 'exec !!! multi_clipboard -I'
    bindkey -m Y eval 'stuff Y' 'writebuf' 'exec !!! multi_clipboard -I'
    bindkey -m W eval 'stuff W' 'writebuf' 'exec !!! multi_clipboard -I'

    # Selection mode
    # C-a a : default is meta (Send the command character (C-a) to window.)
    # C-a C-a : default is other (Toggle the window displayed previously.)
    bind a eval 'command -c mc' 'exec multi_clipboard -S'
    bind ^a eval 'command -c mc' 'exec multi_clipboard -S'

    # Next
    bind -c mc n eval 'command -c mc' 'exec multi_clipboard -S -n'
    bind -c mc ^n eval 'command -c mc' 'exec multi_clipboard -S -n'

    # Previous
    bind -c mc p eval 'command -c mc' 'exec multi_clipboard -S -p'
    bind -c mc ^p eval 'command -c mc' 'exec multi_clipboard -S -p'

    # Quit
    bind -c mc q eval 'exec multi_clipboard -S -q'
    bind -c mc ^q eval 'exec multi_clipboard -S -q'

    # Choose and Quit
    bind -c mc ' ' eval 'exec multi_clipboard -S -s'

    # Launch selection window
    # C-a q : default is xon (Send a control-q to the current window.)
    bind q screen -t multi_clipboard -p - multi_clipboard -W

`SCREENEXCHANGE` is defined in `.bashrc` (see below)
to tell the bufferfile to multi_clipboard.

If you don't set it in `.bashrc`, don't write it in `.screenrc`.
In this case, default file `/tmp/screen-exchange` will be used.

Bindkeys overwrite copy commands in copy mode.
With space/Y/W, it automatically put new clipboard to the clipboards of multi_clipboard.

`selection mode` is started by `a`. (please change it as you like.)

In screen, you can start `selection mode` with `C-a a` or `C-a C-a`.
Then the first clipboard will appear in the message line of screen.

You can change the candidate to next/previous by `n`(`C-n`)/`p`(`C-p`).

Finally, choose the clipboard by `Space`.

Use `q` to stop the mode.


`q` will launch selection window in the new window.
You can choose clipboard by `j`/`k` and select by `Return`.

In this window, you can delete clipboard by `d`.
In addition, you can put the clipboard to snippet by `s`.
(see below Snippet's section.)



## Setup in .bashrc

Add following lines to `.bashrc`.

    export SCREENEXCHANGE=$HOME/.screen-exchange
    export SCREEN_MSGMINWAIT=1
    export CLIPBOARD=$HOME/.clipboard
    export CLMAXHIST=20
    export CLSEP=$'\x07' # $'\x07' is BEL
    export CLX="" #xsel/xclip
    if [[ "$OSTYPE" =~ "linux" ]];then
      if which -s xsel;then
        export CLXOS="xsel"
      elif which -s xsel;then
        export CLXOS="xclip"
      fi
    elif [[ "$OSTYPE" =~ "cygwin" ]];then
      if which -s putclip;then
        export CLXOS="putclip"
      elif which -s xsel;then
        export CLXOS="xsel"
      elif which -s xsel;then
        export CLXOS="xclip"
      fi
    elif [[ "$OSTYPE" =~ "darwin" ]];then
      if which -s pbcopy;then
        export CLXOS="pbcopy"
      fi
    fi

Note 1): SCREENEXCHANGE must be set in .bashrc
         or you must remove the bufferfile definition line from .screenrc
         In the later case, /tmp/screen-exchange will be used.

Note 2): SCREEN_MSGMINWAIT is used to revert msgminwait in screen
         because multi_clipboard temporally change it to 0.
         Default value in both screen and multi_clipboard is 1.
         Therefore, you don't need to set SCREEN_MSGMINWAIT
         unless you set msgminwait in .screenrc.

# Usage

In screen, it automatically add new clipboard in the list.
You can call them from command line by calling multi_clipboard w/o arguments:

    $ multi_clipboard

     3: ccc
     2: bbb
     2: aaa

    choose buffer:


then choose what you want.

Or you can call `selection mode` as described above.

Please see the demo.

![multi_clipboard demo](http://rcmdnk.github.io/images/post/20131204_multi_clipboard.gif)

Example for selection window is available in asciinema:

* [Selection window demo of multi_clipboard](http://asciinema.org/a/6695)


Other command line usages are here:

    $ multi_clipboard -i [args]
    # Push [args]to the clipboard list

    $ multi_clipboard -I
    # Push the screen's clipboard to the clipboard list

    $ multi_clipboard -o
    # Will show the clipboard list, then select one, which will be placed
    # the top of the clipboard list.

    $ multi_clipboard -O
    # Same as -o, in addition, send it to the screen's clipboard

    $ multi_clipboard -s [args]
    # Send [args] to the screen's clipboard

    $ multi_clipboard -S
    # Use in screen for selection mode.

    $ multi_clipboard -w # or w/o any other options
    # Launch selection window

    $ multi_clipboard -W
    # Same as -w, in addition, send it to the screen's clipboard

    $ multi_clipboard -x
    # Send the last clipboard to the clipboard of OS (X server)
    # Even if CLX is not set, it uses CLXOS, is available

    $ multi_clipboard -c
    # Clean up clipboard file

    $ multi_clipboard snippet [option]
    # Use 'snippet' file instead of saved clipboards.

    $ multi_clipboard -h
    # Show this usage

# Snippet

You can use multi_clipboard as snippet manager in addition to clipboard manager.

The default snippet file is `$HOME/.mcsnippet`.
You can change is with `MCSNIPPET` variables in .bashrc like:

    export MCSNIPPET=$HOME/snippets/my_snippet

To use this list, give `snippet` command as a first argument.
Other usage is same as clipboard.

You can put clipboard to snippet from selection window of clipboard
(push `s` on the clipboard which you want to put into snippet).


# References

* [GNU screenでクリップボードの履歴を使えるようにする](http://rcmdnk.github.io/blog/2013/03/24/screen-bash/)
* [GNU screenでクリップボードの履歴を使えるようにする 2](http://rcmdnk.github.io/blog/2013/12/04/computer-screen/)
* [GNU screenでクリップボードの履歴を使えるようにする 3](http://rcmdnk.github.io/blog/2013/12/10/computer-screen/)
