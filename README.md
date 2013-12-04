multi_clipboard
================

Clipboard manager for GNU screen.

# Installation

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

You can also use an install script on the web like:

    $ curl -fsSL https://raw.github.com/rcmdnk/multi_clipboard/install/install.sh| sh

This will install scripts to `/usr/bin`
and you may be asked root password.

If you want to install other directory, do like:

    $ curl -fsSL https://raw.github.com/rcmdnk/multi_clipboard/install/install.sh|  prefix=~/usr/local/ sh

Or, simply download scripts and set where you like.

# Usage

    Multi clipboard manager for GNU screen!
    *** Utility to keep multiple clipboard ***
    *** and manage screen's clipboard.      ***
    
    $ multi_clipboard -i [args]
    # Push [args]to the clipboard list
    
    $ multi_clipboard -I
    # Push the screen's clipboard to the clipboard list
    
    $ multi_clipboard -o
    # Will show the clipboard list, then select one, which will be placed
    # the top of the clipboard list.
    
    $ multi_clipboard -O # (or w/o any other options)
    # Same as -o, in addition, sent it to the screen's clipboard
    
    $ multi_clipboard -s [args]
    # Send [args] to the screen's clipboard
    
    $ multi_clipboard -x
    # Send the last clipboard to the clipboard of OS (X server)
    # Even if CLX is not set, it uses CLXOS, is available
    
    $ multi_clipboard -c
    # Clean up clipboard file
    
    $ multi_clipboard -h
    # Show this usage
    
    
    Settings for screen
    
    To use in screen, put this script where
    PATH is set , and write in .screenrc:
    
    ----------.screenrc---------
    bufferfile "$SCREENEXCHANGE" # SCREENEXCHANGE must be set in .bashrc !!!
    bindkey -m ' ' eval 'stuff \040' 'writebuf' 'exec !!! multi_clipboard -I'
    bindkey -m Y eval 'stuff Y' 'writebuf' 'exec !!! multi_clipboard -I'
    bindkey -m W eval 'stuff W' 'writebuf' 'exec !!! multi_clipboard -I'
    bind a eval 'command -c mc' "!bash -c 'multi_clipboard -S'"
    bind ^a eval 'command -c mc' "!bash -c 'multi_clipboard -S'"
    bind -c mc n eval 'command -c mc' "!bash -c 'multi_clipboard -S -n'"
    bind -c mc p eval 'command -c mc' "!bash -c 'multi_clipboard -S -p'"
    bind -c mc q eval "!bash -c 'multi_clipboard -S -q'"
    bind -c mc ' ' eval "!bash -c 'multi_clipboard -S -s'"
    ----------.screenrc---------

    These settings enable that a clipboard copied by SPACE, Y and  W
    in the copy mode will be sent to the clipboard list.
    If CLX is set, it is also sent to the OS's (X server's) clipboard.

    C-a a (C-a) can be used to select a clipboard from the list,
    instead of using multi_clipboard -O.
    Once you input C-a a, current clipboard is shown in the message line like:

        0: current_clipboard

    You can choose next and previous candidate by n and p, respectively.
    Use space to select a shown clipbaord.
    Use q to quit.

    And set environmental variables in .bashrc

    ----------.bashrc---------
    export SCREENEXCHANGE=$HOME/.screen-exchange
    export SCREEN_MSGMINWAIT=1
    export CLIPBOARD=$HOME/.clipboard
    export CLMAXHIST=20
    export CLSEP="" # "" was inserted with "C-v C-g", use bell as a separator
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
    ----------.bashrc---------

    Note 1): SCREENEXCHANGE must be set in .bashrc
             or you must remove the bufferfile definition line from .screenrc
             In the later case, /tmp/screen-exchange will be used.

    Note 2): SCREEN_MSGMINWAIT is used to revert msgminwait in screen
             because multi_clipboard temporally change it to 0.
             Default value in both screen and multi_clipboard is 1.
             Therefore, you don't need to set SCREEN_MSGMINWAIT
             unless you set msgminwait in .screenrc.

# References

* [GNU screenでクリップボードの履歴を使えるようにする](http://rcmdnk.github.io/blog/2013/03/24/screen-bash/)
