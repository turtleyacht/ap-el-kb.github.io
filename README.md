# ap-el-kb

A mobile keyboard for APL

Visit https://turtleyacht.github.io/ap-el-kb.github.io/ to check it out.

Inspired by pushfoo and 082349872349872 discussion in _The Cognitive Style of
Unix_ (2010) on Hacker News.

## Features

Type in APL on mobile. Receive info about the symbol. Through play,
learn passively various ideas and operations that could filter into your daily
(other) programming languages. Spark curiosity.

Have a place for puzzles, poetry, reference, and general info.

Before we can implement features, we need to be able to type out APL symbols.

## Keyboard Setup (Development Only)

Setup depends on the operating system and defaults. We use OpenBSD on a Unicomp
Classic 101 keyboard (US), which does not have Windows or Menu keys.

Unicode (UTF-8) needs to be enabled. This is required, since the APL symbols are
not accessible by default, and we need additional symbols.

International characters can be produced with the Compose key. We might as well
enable accented letters, currency symbols, and all the diverse characters
outside the ASCII set. Users can type both APL symbols and existing alphabets
without foregoing the latter.

Typing APL should be a "mode" that can be toggled. This sustains flow during
coding.

Combining compose key with mode switch maximizes flexibility. We will likely
need to map (one, mostly two) APL symbols to each letter, number, and special
character on the keyboard--and there are more APL symbols than keys! So, to get
the most allocation up front, we will employ both composition and single-key
(and Shift) techniques.

Of course, after all that, you'll know enough to set up your own preferred way.
That's likely best for a programming language like APL.

For our purposes, we will modify a few files. First, set UTF-8 in ~/.profile:

    export LC_CTYPE=en_US.UTF-8

Next, add a line to ~/.Xdefaults:

    XTerm*locale:utf8

We will take the system default's xinitrc to modify it:

    cp /etc/X11/xinit/xinitrc ~/.xinitrc

The file is a script that is run with `startx`. Near the end of the file, we add
these options:

    setxkbmap -option lv3:ralt_switch_multikey
    setxkbmap -option lv3:caps_switch_latch
    setxkbmap -option grp:ctrl_alt_toggle
    xmodmap $HOME/.Xmodmapapl

Next, copy the .Xmodmapapl file from this repo to your HOME directory.

Finally, type `startx` to start X11 with the modified setup.

Open an `xterm`, and press Left Control and Left Alt at the same time. Typing
1 should print two upper dots, or a _diaeresis,_ and typing exclamation mark
(Shift+1) should print a larger-looking "I" character (called an I-beam).

Your APL input mode is enabled!

Because we take over Left Control + Left Alt, we can't cycle consoles with the
Function keys (F1-F5, for example). This seems okay since we shouldn't be
switching to root too often.

Right Alt + Caps Lock is an ISO_Level_3_Shift, which is an escape route in case
of needing additional Unicode characters (maybe?).

## Verifying APL Symbol Setup

Here are a few test cases to try after launching with `startx`; first, we verify
Compose key works:

1. Press Shift and Right Alt.
2. Type backtick (grave) key.
3. Type `a`

The terminal should produce accented a.

Next, we verify mode switching is enabled:

1. Run `xev -event keyboard`
2. Move pointer to the small window that pops up.
3. Press and hold Left Control.
4. With Left Control pressed, press and hold Left Alt.

This should output "... keycode 64... ISO_Next_Group..."

Now, we can verify one APL symbol and its Shift-assigned keys work:

1. Press Left Control and Left Alt together, and release.
2. Type `a`
3. Type `a` with Shift.
4. Repeat steps 1-3.

Output should be alpha, question mark, lowercase a, and uppercase A.

The test cases may seem extraneous, but the last two are techniques for a
development feedback loop to debug key mappings.

## Brief Usage with got(1) (Development Only)

(More details later.)

1. Configure new SSH key.
2. Add SSH key to git provider via their API.
3. Clone repo and hack away:

    got clone git@ssh_host_alias:turtleyacht/ap-el-kb.github.io.git
    cd ap-el-kb.github.io.git/
    got checkout -b main .

Once cloned and change-dir'd, the current directory has the repo code. As with
git(1) installed on a new machine, some additional config is needed. One is to
add a top-level GOT_AUTHOR directive in got.conf(5):

    author "Your Name <your-email@self-hosted.com>"

Below are some command equivalencies between git(1) and got(1):

    git log                   got log -b | less
    git checkout main         got checkout -b main . # after cd
    git status                got status # after changes
    git status                got branch
    git add _file_            got stage _file_
    git diff --cached         got diff -s
    git commit                got commit -F .commit-template # opens ed(1)
    git push ...              got send

They're not necessarily 1:1 based on implementation, but just by observing what
has changed.

Since ed(1) editor opens by default, it's harder to use 50-char ruler for commit
subject and 72-char ruler for commit body. We use a _commit template_ to make
rulers available.

Learning ed(1) helps one realize code is just text that can be transformed with
patterns.

## Exiting ed(1) (Development Only)

If you're ever stuck, see below steps.

1. Move to the first line with `1`
2. Delete everything with `1,$d`
3. Save the file with `w`
4. Quit with `q`

In sequence, that looks like

    1
    1,$d
    w
    q

This deletes everything in the commit. That will abort the commit message, since
it's now empty.

## Pattern Notes

Reviewing the Unicode characters for APL, the modifiers look to be quad, stile,
underbar, tilde, diaresis (double-quote), macron (overbar), circle, slash,
backslash, jot, minus, and down tack. (More details to be shared in a table
later, once key mappings are complete.)

