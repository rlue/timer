timer – a Pomodoro/Interval Timer 
==================================

**timer** is a command line interval timer written in Bash.

### Features

* Calming, serene alert tone inspired by Buddhist meditation bells
* Set a simple countdown, or specify a set of intervals to time
* Repeat interval sets _n_ times, or indefinitely
* Desktop notification support

### tmux-Aware Interface

In a regular terminal session, remaining time is displayed in the window title.

![](https://raw.githubusercontent.com/rlue/i/master/timer/terminal.gif)

In a tmux session, remaining time is displayed in the status bar beside the session name.

![](https://raw.githubusercontent.com/rlue/i/master/timer/tmux.gif)

### In a custom status bar (_e.g.,_ i3status)

`timer -p` (for “named `p`ipe”) can be used
to feed the countdown to another process via `/tmp/timer.fifo`.

For a crude approach to displaying the remaining time in i3status,
add the following lines to your config file:

```sh
order += "read_file timer"

read_file timer {
	path = "/tmp/timer.fifo"
	format_bad = ""
}
```

> **Note:** This method results in an empty slot (or “module”) in i3bar when
> timer is not in use. A cleaner solution is left as an exercise for the
> reader.
>
> (Hint: See `man i3status`, under “EXTERNAL SCRIPTS/PROGRAMS WITH I3STATUS”.)

Installation
------------

### macOS

via [Homebrew][hb]

```
$ brew install rlue/utils/timer
```

### Linux

#### Dependencies

* bc
* sox
* libsox-fmt-mp3
* libnotify-bin

Download `timer` to a directory on your $PATH (_e.g._, `~/bin`, `~/.local/bin`, `/usr/local/bin`) and make it executable:

```sh
$ curl -o ~/.local/bin/timer https://raw.githubusercontent.com/rlue/timer/master/bin/timer
$ chmod +x ~/.local/bin/timer
```

Usage
-----

### Synopsis

```
$ timer [options] [minutes ...]

    -r rounds                        Repeat timer (n < 0 repeats forever)
    -d seconds                       Delay timer start
    -q                               Suppress command line output
    -N                               Suppress desktop notifications
    -p                               Write to named pipe /tmp/timer.fifo
    -h                               Display this message
    -v                               Display version information
```

Timer duration may be specified in fraction or decimal form; e.g., 90 seconds may be specified as `1.5` or `3/2`.

If multiple durations are specified, an alert will be triggered at the end of each interval.

### Examples

#### Simple timer (30m)

    $ timer 30

#### Meditation timer

From [Quora][qr]:

> Let’s say you meditate for 30 minutes. You can set the interval bell to ring
> after 5 minutes, so you can spend the first 5 minutes settling/relaxing
> yourself and your mind, and then begin the actual meditation practice when
> the interval bell rings.  

    $ timer 5 25

#### Pomodoro technique

From [Wikipedia][pm]:

> 1. Decide on the task to be done.
> 2. Set the pomodoro timer (traditionally to 25 minutes).
> 3. Work on the task until the timer rings.
> 4. After the timer rings, put a checkmark on a piece of paper.
> 5. If you have fewer than four checkmarks, take a short break (3–5 minutes), then go to step 2.
> 6. After four pomodoros, take a longer break (15–30 minutes), reset your checkmark count to zero, then go to step 1.

    $ timer 25 5 25 5 25 5 25 20

Or to repeat this 135-minute set twice in a row,

    $ timer -r2 25 5 25 5 25 5 25 20

### Troubleshooting

timer uses [sox](http://sox.sourceforge.net/) under the hood to chime the
bell. If you receive an error message that begins `play WARN`, it may mean
that sox has selected the wrong audio driver or sound card (“audio device”) to
play on.

When this happens, specifying the appropriate audio driver or device via an
environment variable should fix the problem:

    $ AUDIODRIVER=alsa timer 5

If this works, you can make this setting permanent either by defining a shell
alias

```sh
# ~/.bashrc

alias timer="AUDIODRIVER=alsa timer"
```

or exporting the environment variable globally.

```sh
# ~/.profile

export AUDIODRIVER=alsa
```

License
-------

© 2022 Ryan Lue. This project is licensed under the terms of the MIT license.

[hb]: https://brew.sh/
[pm]: https://en.wikipedia.org/wiki/Pomodoro_Technique#Underlying_principles
[qr]: https://www.quora.com/How-should-one-use-a-meditation-timer-with-an-interval-bell/answer/Phil-Mak
