# How To Set Up a New Mac


## Hello Mac :)
Having just finished setting up my Mac for a second time in as many months, I thought it would be a good time to capture
what I've done to make this process a little easier the next time I'm inevitably going to do it. You could argue that a
better title for this post is "How I Set Up New Mac" since its usefulness is most likely limited to my future self, but
I didn't have a "How I" category on this site...

{{< admonition type=tip open=true >}}
The below post has a lot of code snippets, on load any snippets longer than 20 lines will be in a collapsed state. Feel
free to expand them to see their content 👍
{{< /admonition >}}


## Prerequisites
It should be noted that there is a prerequisite in the form of [Homebrew](https://brew.sh/), I'm not going to detail the
install method here as I don't subscribe to copying vendor documentation, it's another thing I'll need to keep up to date
going forward. Just go ahead and make sure you have configured that before proceeding any further.

Optionally, I also like to install:
* [Oh My Zsh](https://ohmyz.sh/) a framework for the, now, default ZSH shell that comes bundled with Mac OS and offers a
number of plugins and themes for your shell environment.
* [Fira Code](https://github.com/tonsky/FiraCode) a monospaced font with ligatures.
* [Hack](https://sourcefoundry.org/hack/) A typeface designed for source code.

Again, just read the vendor documentation, ok?

## `brew install all-the-things`
Once Homebrew is installed, the rest of my essential apps can be setup using the following:

```shell
brew install \
1password \
alfred \
background-music \
brave-browser \
clipy \
discord \
fzf \
grandperspective \
iterm2 \
itsycal \
joplin \
keepingyouawake \
onedrive \
pwgen \
rectangle \
the_silver_searcher \
trash \
tree \
typora \
visual-studio-code \
zsh-syntax-highlighting
```

{{< admonition type=warning open=true >}}
Once completed, you'll need to install the keybindings for `fzf`, when installing items one at a time it's easy to
spot messages of additional setup steps, however during a bulk install such as that detailed above it is easily missed.
Make sure to run:

`/usr/local/opt/fzf/install`
{{< /admonition >}}

## Pimp My Prompt
At this point I like to make a few adjustments to my shell environment, firstly, a terminal theme.
I use a modified [af-magic theme](https://github.com/ohmyzsh/ohmyzsh/blob/master/themes/af-magic.zsh-theme), the setup I
have configured can be found below:

```shell
# af-magic.zsh-theme
# Repo: https://github.com/andyfleming/oh-my-zsh
# Direct Link: https://github.com/andyfleming/oh-my-zsh/blob/master/themes/af-magic.zsh-theme

if [ $UID -eq 0 ]; then NCOLOR="red"; else NCOLOR="green"; fi
local return_code="%(?..%{$fg[red]%}%? ↵%{$reset_color%})"

# primary prompt
PROMPT='$FG[225]$(date +%H:%M:%S) $my_gray------------------------------------------------------------%{$reset_color%}
$FG[032]%~\
$(git_prompt_info)
$FG[105]%(!.#.»)%{$reset_color%} '
PROMPT2='%{$fg[red]%}\ %{$reset_color%}'
RPS1='${return_code}'

# color vars
eval my_gray='$FG[225]'
eval my_orange='$FG[214]'

# git settings
ZSH_THEME_GIT_PROMPT_PREFIX="$FG[075]($FG[078]"
ZSH_THEME_GIT_PROMPT_CLEAN=""
ZSH_THEME_GIT_PROMPT_DIRTY="$my_orange*%{$reset_color%}"
ZSH_THEME_GIT_PROMPT_SUFFIX="$FG[075])%{$reset_color%}"
```

Save a copy of the above configuration to a file named `af-magic24hr.zsh-theme`, and if it's not within the Oh My Zsh
custom theme directory, move it there

```shell
mv af-magic24hr.zsh-theme ~/.oh-my-zsh/custom/themes/af-magic24hr.zsh-theme
```

Next, we update our `ZSH_THEME` environment variable to point to our new `af-magic24hr` theme, it's easier to do this at
the profile level so that all terminal sessions carry over the setting

```shell
# In your ~/.zshrc file set
ZSH_THEME="af-magic24hr"
```

## Profile Customisations
While editing the zsh profile (`~/.zshrc`), let's add some quality of life settings

```shell
export EDITOR='vim'                     # Set the preferred editor for local and remote sessions
export HISTSIZE=1000000000              # Size of the history kept during a session
export HISTFILESIZE=1000000000          # Size of the history file kept the end of a session
export SAVEHIST=1000000000              # Size of the history file kept the end of a session
export CLICOLOR=1                       # Turns on the ability to use colours in your `ls` output
export LSCOLORS=gxBxhxDxfxhxhxhxhxcxcx  # Tells `ls` what colours to use
```

Also, let's add some shortcuts to help with day to day operations.

```shell
alias finder='open -a Finder ./'
alias flush='dscacheutil -flushcache && killall -HUP mDNSResponder'
alias cg='cd ~/gitspace'
alias ga='git add'
alias gd='git diff'
alias gs='git status'
alias less='less -FSRXc'
alias ll='ls -GFlAhp'
alias ls='ls -GF'
alias mkdir='mkdir -pv'
alias mv='mv -iv'
alias zsource='source /Users/${LOGNAME}/.zshrc'
alias zvim='vim /Users/${LOGNAME}/.zshrc'
```

I've not provided an explanation of each of the aliases, but they are pretty easy to test to see if you like them.
Having said that, I highly recommend the following as they have simplified my workflow between UI and CLI.

```shell
alias finder='open -a Finder ./'    # Opens a Finder window to the directory you're in on your shell
alias mkdir='mkdir -pv'             # Makes directories, including any intermediate directories and prints this output
alias mv='mv -iv'                   # Moves files, asking for confirmation and being verbose about things
```

Finally, and assuming you installed the `zsh-syntax-highlighting` package mentioned earlier in the post, drop this line
at the end of your `zshrc` profile.

```shell
source /usr/local/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

{{< admonition type=quote open=true >}}
It enables highlighting of commands whilst they are typed at a zsh prompt into an interactive terminal. This helps in reviewing commands before running them, particularly in catching syntax errors.
{{< /admonition >}}

## Lightweight Vim Customisations
At this point we can save changes to the profile and move onto finishing the rest of our setup. I mentioned earlier that
I was setting my preferred editor to Vim, if you don't use Vim skip ahead.

For the most part, I use a pretty stock install of Vim, there are some very small improvements to the default Vim
configuration which I add as I think it makes the viewport of the editor more useful. The below can be copied to the
`~/.vimrc` file, and if one doesn't exit, you can create it. Some of the key changes are the support for highlighting
your cursorline, syntax highlighting for a variety of languages, character column highlight and sets line numbers by default.

```
" Attempt to determine the type of a file based on its name and possibly its
" contents. Use this to allow intelligent auto-indenting for each filetype,
" and for plugins that are filetype specific.
filetype indent plugin on

" Enable syntax highlighting
syntax on

" Use case insensitive search, except when using capital letters
set ignorecase
set smartcase

" Use visual bell instead of beeping when doing something wrong
set visualbell

" Display line numbers on the left
set number

" Set colour line marker to show 100 character of a line
set colorcolumn=100

" When opening a new line and no filetype-specific indenting is enabled, keep
" the same indent as the line you're currently on. Useful for READMEs, etc.
set autoindent

" Stop certain movements from always going to the first character of a line.
" While this behaviour deviates from that of Vi, it does what most users
" coming from other editors would expect.
set nostartofline

filetype plugin indent on
" show existing tab with 4 spaces width
set tabstop=4
" when indenting with '>', use 4 spaces width
set shiftwidth=4
" On pressing tab, insert 4 spaces
set expandtab

" This setting allows you to highlight all instances of the word under your
" cursor, press *.
set hlsearch

" This highlights trailing whitespace
match ErrorMsg '\s\+$'

" Set cursorline to on "
set cursorline

" Set characters to identify tabs "
set lcs=tab:▸\ ,nbsp:_
set list
```

These customisations barely scratch the surface of what is possible with Vim, to open that can of worms just search for "vim plugin managers".

## Customise iTerm
Finally, before I can get started with using my machine there is the small issue of the iTerm2 profile.
I have an iTerm profile I set up many years ago, I have no idea what is really contained within the profile that isn't
a default setting at this point in time. All I know is I need to import this file on every new machine I use.
For completion, I'm dropping the code for that profile here but unless you love a hot pink box cursor and a Dracula-esque
theme, it's probably not going to be of much use to you.

```
{
  "Ansi 2 Color" : {
    "Green Component" : 0.98039215686274506,
    "Blue Component" : 0.4823529411764706,
    "Red Component" : 0.31372549019607843
  },
  "Tags" : [

  ],
  "Ansi 12 Color" : {
    "Green Component" : 0.57647058823529407,
    "Blue Component" : 0.97647058823529409,
    "Red Component" : 0.74117647058823533
  },
  "Has Hotkey" : false,
  "Ansi 7 Color" : {
    "Green Component" : 0.73333334922790527,
    "Blue Component" : 0.73333334922790527,
    "Red Component" : 0.73333334922790527
  },
  "Bold Color" : {
    "Green Component" : 1,
    "Blue Component" : 1,
    "Red Component" : 1
  },
  "Ansi 8 Color" : {
    "Green Component" : 0.33333333333333331,
    "Blue Component" : 0.33333333333333331,
    "Red Component" : 0.33333333333333331
  },
  "Ansi 9 Color" : {
    "Green Component" : 0.33333333333333331,
    "Blue Component" : 0.33333333333333331,
    "Red Component" : 1
  },
  "Ansi 6 Color" : {
    "Green Component" : 0.9137254901960784,
    "Blue Component" : 0.99215686274509807,
    "Red Component" : 0.54509803921568623
  },
  "Rows" : 24,
  "Default Bookmark" : "No",
  "Ansi 4 Color" : {
    "Green Component" : 0.57647058823529407,
    "Blue Component" : 0.97647058823529409,
    "Red Component" : 0.74117647058823533
  },
  "Cursor Guide Color" : {
    "Red Component" : 0.70213186740875244,
    "Color Space" : "sRGB",
    "Blue Component" : 1,
    "Alpha Component" : 0.25,
    "Green Component" : 0.9268307089805603
  },
  "Non-ASCII Anti Aliased" : true,
  "Use Bright Bold" : true,
  "Ansi 10 Color" : {
    "Green Component" : 0.98039215686274506,
    "Blue Component" : 0.4823529411764706,
    "Red Component" : 0.31372549019607843
  },
  "Use Cursor Guide" : false,
  "Ambiguous Double Width" : false,
  "Jobs to Ignore" : [
    "rlogin",
    "ssh",
    "slogin",
    "telnet"
  ],
  "Show Status Bar" : true,
  "Ansi 15 Color" : {
    "Green Component" : 1,
    "Blue Component" : 1,
    "Red Component" : 1
  },
  "Foreground Color" : {
    "Green Component" : 0.97254902124404907,
    "Blue Component" : 0.94901961088180542,
    "Red Component" : 0.97254902124404907
  },
  "Bound Hosts" : [

  ],
  "Working Directory" : "\/Users\/peter",
  "Blinking Cursor" : false,
  "Disable Window Resizing" : true,
  "Sync Title" : false,
  "Prompt Before Closing 2" : 1,
  "BM Growl" : true,
  "Command" : "",
  "Description" : "Default",
  "Smart Cursor Color" : false,
  "Mouse Reporting" : true,
  "Screen" : -1,
  "Selection Color" : {
    "Green Component" : 0.27843138575553894,
    "Blue Component" : 0.35294118523597717,
    "Red Component" : 0.26666668057441711
  },
  "Columns" : 80,
  "Idle Code" : 0,
  "Ansi 13 Color" : {
    "Green Component" : 0.47450980392156861,
    "Blue Component" : 0.77647058823529413,
    "Red Component" : 1
  },
  "Custom Command" : "No",
  "ASCII Anti Aliased" : true,
  "Non Ascii Font" : "Monaco 12",
  "Vertical Spacing" : 1,
  "Use Bold Font" : true,
  "Option Key Sends" : 2,
  "Selected Text Color" : {
    "Green Component" : 1,
    "Blue Component" : 1,
    "Red Component" : 1
  },
  "Background Color" : {
    "Green Component" : 0.12215272337198257,
    "Blue Component" : 0.15977837145328522,
    "Red Component" : 0.11765811592340469
  },
  "Character Encoding" : 4,
  "Ansi 11 Color" : {
    "Green Component" : 0.98039215686274506,
    "Blue Component" : 0.5490196078431373,
    "Red Component" : 0.94509803921568625
  },
  "Use Italic Font" : true,
  "Unlimited Scrollback" : true,
  "Keyboard Map" : {
    "0xf700-0x260000" : {
      "Text" : "[1;6A",
      "Action" : 10
    },
    "0x37-0x40000" : {
      "Text" : "0x1f",
      "Action" : 11
    },
    "0x32-0x40000" : {
      "Text" : "0x00",
      "Action" : 11
    },
    "0xf709-0x20000" : {
      "Text" : "[17;2~",
      "Action" : 10
    },
    "0xf70c-0x20000" : {
      "Text" : "[20;2~",
      "Action" : 10
    },
    "0xf729-0x20000" : {
      "Text" : "[1;2H",
      "Action" : 10
    },
    "0xf72b-0x40000" : {
      "Text" : "[1;5F",
      "Action" : 10
    },
    "0xf705-0x20000" : {
      "Text" : "[1;2Q",
      "Action" : 10
    },
    "0xf703-0x260000" : {
      "Text" : "[1;6C",
      "Action" : 10
    },
    "0xf700-0x220000" : {
      "Text" : "[1;2A",
      "Action" : 10
    },
    "0xf701-0x280000" : {
      "Text" : "0x1b 0x1b 0x5b 0x42",
      "Action" : 11
    },
    "0x38-0x40000" : {
      "Text" : "0x7f",
      "Action" : 11
    },
    "0x33-0x40000" : {
      "Text" : "0x1b",
      "Action" : 11
    },
    "0xf703-0x220000" : {
      "Text" : "[1;2C",
      "Action" : 10
    },
    "0xf701-0x240000" : {
      "Text" : "[1;5B",
      "Action" : 10
    },
    "0xf70d-0x20000" : {
      "Text" : "[21;2~",
      "Action" : 10
    },
    "0xf702-0x260000" : {
      "Text" : "[1;6D",
      "Action" : 10
    },
    "0xf729-0x40000" : {
      "Text" : "[1;5H",
      "Action" : 10
    },
    "0xf706-0x20000" : {
      "Text" : "[1;2R",
      "Action" : 10
    },
    "0x34-0x40000" : {
      "Text" : "0x1c",
      "Action" : 11
    },
    "0xf700-0x280000" : {
      "Text" : "0x1b 0x1b 0x5b 0x41",
      "Action" : 11
    },
    "0x2d-0x40000" : {
      "Text" : "0x1f",
      "Action" : 11
    },
    "0xf70e-0x20000" : {
      "Text" : "[23;2~",
      "Action" : 10
    },
    "0xf702-0x220000" : {
      "Text" : "[1;2D",
      "Action" : 10
    },
    "0xf703-0x280000" : {
      "Text" : "f",
      "Action" : 10
    },
    "0xf700-0x240000" : {
      "Text" : "[1;5A",
      "Action" : 10
    },
    "0xf707-0x20000" : {
      "Text" : "[1;2S",
      "Action" : 10
    },
    "0xf70a-0x20000" : {
      "Text" : "[18;2~",
      "Action" : 10
    },
    "0x35-0x40000" : {
      "Text" : "0x1d",
      "Action" : 11
    },
    "0xf70f-0x20000" : {
      "Text" : "[24;2~",
      "Action" : 10
    },
    "0xf703-0x240000" : {
      "Text" : "[1;5C",
      "Action" : 10
    },
    "0xf701-0x260000" : {
      "Text" : "[1;6B",
      "Action" : 10
    },
    "0xf702-0x280000" : {
      "Text" : "b",
      "Action" : 10
    },
    "0xf72b-0x20000" : {
      "Text" : "[1;2F",
      "Action" : 10
    },
    "0x36-0x40000" : {
      "Text" : "0x1e",
      "Action" : 11
    },
    "0xf708-0x20000" : {
      "Text" : "[15;2~",
      "Action" : 10
    },
    "0xf701-0x220000" : {
      "Text" : "[1;2B",
      "Action" : 10
    },
    "0xf70b-0x20000" : {
      "Text" : "[19;2~",
      "Action" : 10
    },
    "0xf702-0x240000" : {
      "Text" : "[1;5D",
      "Action" : 10
    },
    "0xf704-0x20000" : {
      "Text" : "[1;2P",
      "Action" : 10
    }
  },
  "Window Type" : 0,
  "Cursor Boost" : 0,
  "Cursor Type" : 2,
  "Background Image Location" : "",
  "Blur" : false,
  "Badge Color" : {
    "Red Component" : 1,
    "Color Space" : "sRGB",
    "Blue Component" : 0,
    "Alpha Component" : 0.5,
    "Green Component" : 0.1491314172744751
  },
  "Scrollback Lines" : 0,
  "Send Code When Idle" : false,
  "Close Sessions On End" : true,
  "Terminal Type" : "xterm-256color",
  "Visual Bell" : true,
  "Flashing Bell" : true,
  "Status Bar Layout" : {
    "components" : [
      {
        "class" : "iTermStatusBarClockComponent",
        "configuration" : {
          "knobs" : {
            "localize" : 1,
            "base: priority" : 5,
            "base: compression resistance" : 1,
            "shared text color" : {
              "Red Component" : 0.90000000000000002,
              "Color Space" : "sRGB",
              "Blue Component" : 0.63,
              "Alpha Component" : 1,
              "Green Component" : 0.63
            },
            "format" : "dd-MMM kk:mm"
          },
          "layout advanced configuration dictionary value" : {
            "font" : ".AppleSystemUIFont 12",
            "algorithm" : 0
          }
        }
      },
      {
        "class" : "iTermStatusBarCPUUtilizationComponent",
        "configuration" : {
          "knobs" : {
            "base: priority" : 5,
            "base: compression resistance" : 1,
            "shared text color" : {
              "Red Component" : 0.84599999999999997,
              "Color Space" : "sRGB",
              "Blue Component" : 0.63,
              "Alpha Component" : 1,
              "Green Component" : 0.90000000000000002
            }
          },
          "layout advanced configuration dictionary value" : {
            "font" : ".AppleSystemUIFont 12",
            "algorithm" : 0
          }
        }
      },
      {
        "class" : "iTermStatusBarMemoryUtilizationComponent",
        "configuration" : {
          "knobs" : {
            "base: priority" : 5,
            "base: compression resistance" : 1,
            "shared text color" : {
              "Red Component" : 0.63,
              "Color Space" : "sRGB",
              "Blue Component" : 0.7380000000000001,
              "Alpha Component" : 1,
              "Green Component" : 0.90000000000000002
            }
          },
          "layout advanced configuration dictionary value" : {
            "font" : ".AppleSystemUIFont 12",
            "algorithm" : 0
          }
        }
      },
      {
        "class" : "iTermStatusBarNetworkUtilizationComponent",
        "configuration" : {
          "knobs" : {
            "base: priority" : 5,
            "shared text color" : {
              "Red Component" : 0.63,
              "Color Space" : "sRGB",
              "Blue Component" : 0.90000000000000002,
              "Alpha Component" : 1,
              "Green Component" : 0.73799999999999988
            },
            "base: compression resistance" : 1
          },
          "layout advanced configuration dictionary value" : {
            "font" : ".AppleSystemUIFont 12",
            "algorithm" : 0
          }
        }
      },
      {
        "class" : "iTermStatusBarHostnameComponent",
        "configuration" : {
          "knobs" : {
            "path" : "hostname",
            "base: priority" : 5,
            "base: compression resistance" : 1,
            "shared text color" : {
              "Red Component" : 0.8460000000000002,
              "Color Space" : "sRGB",
              "Blue Component" : 0.90000000000000002,
              "Alpha Component" : 1,
              "Green Component" : 0.63
            }
          },
          "layout advanced configuration dictionary value" : {
            "font" : ".AppleSystemUIFont 12",
            "algorithm" : 0
          }
        }
      }
    ],
    "advanced configuration" : {
      "font" : ".AppleSystemUIFont 12",
      "algorithm" : 0
    }
  },
  "Silence Bell" : true,
  "Ansi 14 Color" : {
    "Green Component" : 0.9137254901960784,
    "Blue Component" : 0.99215686274509807,
    "Red Component" : 0.54509803921568623
  },
  "ASCII Ligatures" : true,
  "Name" : "Skippy Dark",
  "Cursor Text Color" : {
    "Green Component" : 1,
    "Blue Component" : 1,
    "Red Component" : 1
  },
  "Minimum Contrast" : 0,
  "Shortcut" : "8",
  "Cursor Color" : {
    "Red Component" : 1,
    "Color Space" : "sRGB",
    "Blue Component" : 0.72322267293930054,
    "Alpha Component" : 1,
    "Green Component" : 0.20634862780570984
  },
  "Transparency" : 0,
  "Guid" : "FB057361-34C5-04G1-23D5-6CF3246C1111",
  "Horizontal Spacing" : 1,
  "Ansi 3 Color" : {
    "Green Component" : 0.98039215686274506,
    "Blue Component" : 0.5490196078431373,
    "Red Component" : 0.94509803921568625
  },
  "Link Color" : {
    "Red Component" : 0,
    "Color Space" : "sRGB",
    "Blue Component" : 0.73423302173614502,
    "Alpha Component" : 1,
    "Green Component" : 0.35916060209274292
  },
  "Ansi 5 Color" : {
    "Green Component" : 0.47450980392156861,
    "Blue Component" : 0.77647058823529413,
    "Red Component" : 1
  },
  "Use Non-ASCII Font" : false,
  "Right Option Key Sends" : 0,
  "Normal Font" : "FiraCode-SemiBold 15",
  "Ansi 0 Color" : {
    "Green Component" : 0,
    "Blue Component" : 0,
    "Red Component" : 0
  },
  "Ansi 1 Color" : {
    "Green Component" : 0.3333333432674408,
    "Blue Component" : 0.3333333432674408,
    "Red Component" : 1
  },
  "Custom Directory" : "Recycle"
}
```

I think that's about it, in a future post, I'm going to take a bit of time and give an overview of each of the tools
I installed at the start of this guide, it has become clear when discussing with friends and colleagues that I somehow
stumble across a variety of tools which people find useful yet haven't a clue they exist. For now though, I have a guide
that I can follow on my next rebuild to get the scaffolding I require for my Mac in place fairly easily.

And with that, thanks for reading.

