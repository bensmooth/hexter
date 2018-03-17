                                hexter
                                ======
               A Yamaha DX7 modeling software synthesizer
                   for the DSSI Soft Synth Interface

Introduction
============
hexter is a software synthesizer that models the sound generation of
a Yamaha DX7 synthesizer.  It can easily load most DX7 patch bank
files, edit those patches via a built-in editor or MIDI sys-ex
messages (ALSA systems only), and recreate the sound of the DX7 with
greater accuracy than any previous open-source emulation (that the
author is aware of....)

hexter operates as a plugin for the DSSI Soft Synth Interface.  DSSI
is a plugin API for software instruments (soft synths) with user
interfaces, permitting them to be hosted in-process by audio
applications.  More information on DSSI can be found at:

  http://dssi.sourceforge.net/

hexter is written and copyright (c) 2014 by Sean Bolton, and
licensed under the GNU General Public License, version 2 or later.
See the enclosed file COPYING for details.  While this software is
'free' within the requirements of this license, I (Sean) would
appreciate any or all of the following should you find hexter
useful:
    - an email stating where you're from and how you're using
        hexter, sent to <sean /at/ smbolton /dot/ com>.
    - copies of or links to music you've created with hexter.
    - any favorite patches you create for hexter, or any patches
        that work on a real DX7 but not on hexter.
    - suggestions for improving hexter.

hexter is indirectly the result of cumulative effort by a large
number of contributors, see the AUTHORS file for details.

Requirements
============
hexter requires the following:

    - DSSI version 0.4 or greater, available from the
        dssi.sourceforge.net address above.

    - liblo version 0.12 or greater (0.23 or greater recommended), a
        library implementing the Open Sound Control (OSC) protocol,
        available at:

        http://liblo.sourceforge.net/

    - pkgconfig with PKG_CONFIG_PATH set appropriately to pick up
        DSSI and liblo.

    - GTK+ version 2.4 or later.

    - the LADSPA v1.x SDK.

    - the ALSA headers (DSSI plugins use ALSA structures, but not
        the actual drivers, so you don't necessarily need the
        drivers installed.)  Users of non-ALSA systems may use
        libdssialsacompat, available at:

        http://smbolton.com/linux.html

    - a working DSSI host.  hexter has been tested with the
        jack-dssi-host, available in the DSSI distribution, and with
        ghostess, available at:

        http://smbolton.com/linux.html

    - automake 1.7 and autoconf 2.57 or better if you wish to
        recreate the build files.

Installation
============
The generic installation instructions in the enclosed file INSTALL
aren't particularly helpful, so try this instead:

1. Unpack the tar file.

2. Make sure PKG_CONFIG_PATH is set correctly to locate the
    dssi.pc and liblo.pc pkgconfig files.  On many systems, this
    will be:

    $ PKG_CONFIG_PATH=/usr/local/lib/pkgconfig
    $ export PKG_CONFIG_PATH

3. 'cd' into the package directory and execute './configure'.
    configure will add '-Wall' and my favorite optimizations to
    CFLAGS for you if you don't include any '-Wall' or '-O' options.
    If you're using gcc and wish to provide your own optimization
    flags, you MUST at least use '-finline' and a non-zero '-O' flag
    to get decent results.

    Depending on your hardware, you may wish to add the
    '--enable-floating-point' option to the configure cammand line.
    See the 'Fixed Point vs. Floating Point Rendering' section
    below. (If you're not sure, it is safest to leave it off.)

4. Enable debugging information if you desire: edit the file
    src/hexter.h, and define DSSP_DEBUG as explained in the
    comments.

5. Do 'make'.  Hopefully it should build without warnings (or
    errors.)

6. 'make install' will install the following:

    <prefix>/lib/dssi/hexter/hexter_gtk
    <prefix>/lib/dssi/hexter.so
    <prefix>/lib/dssi/hexter.la
    <prefix>/share/hexter/dx7_roms.dx7
    <prefix>/share/hexter/fb01_roms_converted_12.dx7
    <prefix>/share/hexter/fb01_roms_converted_34.dx7
    <prefix>/share/hexter/fb01_roms_converted_5.dx7
    <prefix>/share/hexter/tx7_roms.dx7

Feedback on your experiences building hexter is appreciated.

Operation
=========
To run the hexter plugin under the jack-dssi-host provided
in the DSSI distribution, I do the following:

1. Start JACK.

2. Start jack-dssi-host, supplying the correct plugin path
    and filename (substitute <prefix> as appropriate):

    $ DSSI_PATH=<prefix>/lib/dssi sudo jack-dssi-host hexter.so

    jack-dssi-host should start, and moments later the hexter
    graphic user interface should appear.

3. Use qjackctl or aconnect to connect jack-dssi-host to a MIDI
   source, such as vkeybd.

4. Begin playing sounds!  If you get no response, try clicking the
    'Send Test Note' button in the hexter GUI.  This sends a note
    directly via the host to the plugin, so if you hear sound now,
    look for a problem between the host and your MIDI source.  If
    you still don't hear sound, I'd look for a problem between the
    host and your output device.  If you continue having trouble,
    you might recompile with DSSP_DEBUG bit 2 set, which will cause
    the plugin to continuously output a quiet buzz to help debug
    your outgoing signal path.

hexter starts up with a default set of sound presets, or 'patches',
that you can select either by selecting the GUI 'Patches' tab and
clicking on the patch name, or by sending a MIDI program change from
your MIDI source with the appropriate program number.

Patch Import/Export
===================
hexter can import DX7 patch bank files in several formats:

- The standard 32-voice bulk dump sys-ex format.  These are 4104
bytes long, and you can find a million of them by Googling for "dx7
patches".  hexter places no restrictions on their filenames.

- The standard single-voice (edit buffer) sys-ex format.  These are
163 bytes long.

- Raw packed patch data. These are 128 bytes per patch, and may
contain any number of patches (although hexter can only load up to
128 of them.) Note, though, that if the file is 8192 bytes long and
has any of the filename suffixes ".tx7", ".TX7", ".snd", or ".SND",
the second half of the file will be ignored (Dr. T and Steinberg TX7
formats).

- Standard MIDI files containing either a 32-voice bulk dump sys-ex
message or a single voice dump sys-ex message.  If the file contains
more than one qualifying sys-ex message, only the first will be used.

- Two concatenated 32-voice bulk dump sys-ex messages (8208 bytes
long.)

- Steinberg Synthworks format (5216 bytes long).

- Transform XSyn format (8192 bytes long).

- Voyetra SIDEMAN DX/TX and Patchmaster DX7/TX7 formats (9816
or 5663 bytes long).

- Yamaha DX200 editor format (326454 bytes long).</p>

You may use the "Import Patch Bank..." option in the File menu to
import patches.  After selecting the filename, you will need to
specify the program number (0 to 127) at which to begin importing
the patches.

Several patch bank files are installed with hexter in
<prefix>/share/hexter/.

You may also export patch banks using the File menu "Export Patch
Bank..." option.  You can then select which of the formats to save
in, and what range of patches to export.  Finally, select the
filename to which you want to save, and click 'Ok'.

Synthesizer Configuration
=========================
On the 'Configuration' tab of the hexter GUI,  there are a number of
controls for configuring hexter:

- tuning: sets the tuning of this instance of the plugin,
    as Hz for A-above-middle-C.

- volume: adjusts the output volume for this instance, from -70dB to
    +20dB relative to a nominal reference level (-18.1dB per voice,
    which permits approximately 8 voice polyphony within -1.0 to
    +1.0 float.)

- polyphony (instance): Sets the maximum polyphony for this instance
    of the plugin.  If you attempt to play more notes than this
    setting, already-playing notes will be killed so that newer
    notes can be played.

- polyphony (global): Sets the maximum polyphony for all hexter
    instances running on this host.  If you are getting xruns, try
    reducing this setting.

- monophonic modes: sets poly/mono operation for this instance of
    the plugin to one of the following:
    'Off'  - polyphonic operation.
    'On'   - monophonic operation, where the envelopes are
             retriggered upon each incoming note on event.
    'Once' - monophonic operation, where the envelopes are triggered
             only on the first note on of a legato phrase -- that
             is, if you hold one note while playing a second, the
             envelopes will not be retriggered for the second note.
    'Both' - monophonic operation, where the envelopes are
             retriggered at each note on, and upon note off when
             other keys are still held.

- disable LFO/Mod/Perf (0.5.x compatibility): selecting this check
    box will disable the LFO, amplitude modulation, pitch
    modulation, and performance parameter enhancements provided by
    hexter 0.6.0 and later versions, forcing it to sound just like
    the 0.5.9 version.  This is provided for backward compatibility
    with existing projects that used hexter 0.5.9.

- Sys-Ex Patch Editing: On ALSA systems, hexter has the ability to
    receive MIDI system exclusive messages from an external patch
    editor/librarian. Any patch edits received are reflected in
    hexter's built-in patch editor, and must be saved using the
    built-in editor's 'Save Changes into Patch Bank' button (see
    below).

    Sys-ex patch editing is enabled on the 'Configuration' tab by
    depressing the 'Enable Sys-Ex Editing' button.  The hexter GUI
    then creates an ALSA MIDI client and displays its client and
    port number in the status window below the 'Sys-Ex Receive
    Channel' slider.  Set the channel appropriately and route your
    external patch editor to this port using aconnect or similar,
    and the hexter instance will be ready to receive patch edits.

    Note that hexter only _receives_ MIDI sys-ex messages, and does
    not send them.  Also, it only receives single patch dumps and
    voice parameter changes, so any 'get patch data from synth' or
    'send 32 voices to synth' functions of your librarian will not
    work.

Performance Parameters
======================
The 'Performance' tab of the hexter GUI contains controls for the
DX7/TX7 Performance Parameters:

- pitch bend range: sets the response to MIDI pitch bend messages,
    in semitones.

- portamento time: sets the portamento time.  Note that as of
    version 0.6.1, the portamento code is still unfinished.

- mod wheel sensitivity: sets the depth of response to MIDI
    modulation wheel control change messages (MIDI control change 1).

- mod wheel assign: these three check boxes set which destinations
    the modulation wheel affects: selecting 'P' routes the LFO to
    pitch modulation (vibrato), 'A' routes the LFO to amplitude
    modulation (tremelo or wah), and 'E' routes the modulation wheel
    value itself to amplitude modulation.

- foot sensitivity, foot assign, pressure sens., pressure assign,
    breath sens., and breath assign: these set the sensitivity and
    routing as described above for the foot controller (MIDI CC 4),
    for pressure (both channel and key pressure), and for the breath
    controller (CC 2), respectively.

Patch Editing
=============
The current patch may be edited by selecting 'Edit Patch...' from
the 'Edit' menu, which opens the patch editor window. All patch
edits accumulate as a temporary 'overlay' replacing the current
patch, but are not saved into the patch bank until you click the
'Save Changes into Patch Bank' button and complete the save process.
(Once you've saved edits into the patch bank, remember to save the
bank to disk using the 'Export Patch Bank...' option of the 'File'
menu!)

Clicking the 'Discard Changes' button or selecting another patch
from the 'Patches' tab will discard any active edits. At any time
the top status line of the editor window will tell you which patch
is being edited, and whether there are any changes in effect.

The editor has two modes of operation, selected by the 'Editor Mode'
combo box near the bottom left of the editor window. One mode,
called 'Widgy', uses standard GTK+ widgets for editing patch
parameters, and displays graphical representations of envelopes and
scaling curves to aid in comprehension of the patch parameters. The
other mode, called 'Retro' is based on text-based editors of decades
past. You may switch between editor modes at any time.

While the 'Retro' mode provides little in terms of visualization
assistance, it can provide the experienced user with more efficient
editing. Both the mouse and cursor keys may be used to select the
parameter to be edited. Generally, the number keys are used to enter
a parameter directly, the '-' key decrements a parameter, the '+' or
'=' keys increment the parameter, and the 'delete' or 'backspace'
key will reset the parameter to a default value. Perhaps most
convient for users without a separate MIDI keyboard, the space bar
can be used to toggle a test note, even while changing patches with
other keys!

MIDI Controller Mapping
=======================
In addition to the performance parameter MIDI messages mentioned
above, hexter responds to MIDI volume (MIDI control change 39),
sustain pedal (MIDI CC 64), and all-sounds-off, all-notes-off, and
reset-controllers control messages (CCs 120, 123, and 121,
respectively).

The operator parameters of the current patch can also be changed via
the following MIDI control change (CC) and non-registered parameter
(NRPN) messages. Messages marked with '*' in the 'Operator' column
will cause an immediate effect on playing notes, while the others
will only affect subsequently-played notes.

CC or NRPN                     | Operator | Parameter
-------------------------------+----------+------------------------
CC 16 (General Purpose #1 MSB) |   * 1    | Frequency Coarse
CC 17 (General Purpose #2 MSB) |   * 2    | Frequency Coarse
CC 18 (General Purpose #3 MSB) |   * 3    | Frequency Coarse
CC 19 (General Purpose #4 MSB) |   * 4    | Frequency Coarse
CC 80 (General Purpose #5)     |   * 5    | Frequency Coarse
CC 81 (General Purpose #6)     |   * 6    | Frequency Coarse
NRPN   0                       |     6    | Envelope Generator Rate 1
NRPN   1                       |     6    | Envelope Generator Rate 2
NRPN   2                       |     6    | Envelope Generator Rate 3
NRPN   3                       |     6    | Envelope Generator Rate 4
NRPN   4                       |     6    | Envelope Generator Level 1
NRPN   5                       |     6    | Envelope Generator Level 2
NRPN   6                       |     6    | Envelope Generator Level 3
NRPN   7                       |     6    | Envelope Generator Level 4
NRPN   8                       |     6    | Keyboard Level Scaling Break Point
NRPN   9                       |     6    | Keyboard Level Scaling Left Depth
NRPN  10                       |     6    | Keyboard Level Scaling Right Depth
NRPN  11                       |     6    | Keyboard Level Scaling Left Curve
NRPN  12                       |     6    | Keyboard Level Scaling Right Curve
NRPN  13                       |     6    | Keyboard Rate Scaling
NRPN  14                       |     6    | Amp Mod Sensitivity
NRPN  15                       |     6    | Keyboard Velocity Sensitivity
NRPN  16                       |     6    | Operator Output Level
NRPN  17                       |   * 6    | Oscillator Mode
NRPN  18                       |   * 6    | Oscillator Frequency Coarse
NRPN  19                       |   * 6    | Oscillator Frequency Fine
NRPN  20                       |   * 6    | Oscillator Detune
NRPN  21                       |     5    | Envelope Generator Rate 1
NRPN  22                       |     5    | Envelope Generator Rate 2
NRPN  23                       |     5    | Envelope Generator Rate 3
NRPN  24                       |     5    | Envelope Generator Rate 4
NRPN  25                       |     5    | Envelope Generator Level 1
NRPN  26                       |     5    | Envelope Generator Level 2
NRPN  27                       |     5    | Envelope Generator Level 3
NRPN  28                       |     5    | Envelope Generator Level 4
NRPN  29                       |     5    | Keyboard Level Scaling Break Point
NRPN  30                       |     5    | Keyboard Level Scaling Left Depth
NRPN  31                       |     5    | Keyboard Level Scaling Right Depth
NRPN  32                       |     5    | Keyboard Level Scaling Left Curve
NRPN  33                       |     5    | Keyboard Level Scaling Right Curve
NRPN  34                       |     5    | Keyboard Rate Scaling
NRPN  35                       |     5    | Amp Mod Sensitivity
NRPN  36                       |     5    | Keyboard Velocity Sensitivity
NRPN  37                       |     5    | Operator Output Level
NRPN  38                       |   * 5    | Oscillator Mode
NRPN  39                       |   * 5    | Oscillator Frequency Coarse
NRPN  40                       |   * 5    | Oscillator Frequency Fine
NRPN  41                       |   * 5    | Oscillator Detune
NRPN  42                       |     4    | Envelope Generator Rate 1
NRPN  43                       |     4    | Envelope Generator Rate 2
NRPN  44                       |     4    | Envelope Generator Rate 3
NRPN  45                       |     4    | Envelope Generator Rate 4
NRPN  46                       |     4    | Envelope Generator Level 1
NRPN  47                       |     4    | Envelope Generator Level 2
NRPN  48                       |     4    | Envelope Generator Level 3
NRPN  49                       |     4    | Envelope Generator Level 4
NRPN  50                       |     4    | Keyboard Level Scaling Break Point
NRPN  51                       |     4    | Keyboard Level Scaling Left Depth
NRPN  52                       |     4    | Keyboard Level Scaling Right Depth
NRPN  53                       |     4    | Keyboard Level Scaling Left Curve
NRPN  54                       |     4    | Keyboard Level Scaling Right Curve
NRPN  55                       |     4    | Keyboard Rate Scaling
NRPN  56                       |     4    | Amp Mod Sensitivity
NRPN  57                       |     4    | Keyboard Velocity Sensitivity
NRPN  58                       |     4    | Operator Output Level
NRPN  59                       |   * 4    | Oscillator Mode
NRPN  60                       |   * 4    | Oscillator Frequency Coarse
NRPN  61                       |   * 4    | Oscillator Frequency Fine
NRPN  62                       |   * 4    | Oscillator Detune
NRPN  63                       |     3    | Envelope Generator Rate 1
NRPN  64                       |     3    | Envelope Generator Rate 2
NRPN  65                       |     3    | Envelope Generator Rate 3
NRPN  66                       |     3    | Envelope Generator Rate 4
NRPN  67                       |     3    | Envelope Generator Level 1
NRPN  68                       |     3    | Envelope Generator Level 2
NRPN  69                       |     3    | Envelope Generator Level 3
NRPN  70                       |     3    | Envelope Generator Level 4
NRPN  71                       |     3    | Keyboard Level Scaling Break Point
NRPN  72                       |     3    | Keyboard Level Scaling Left Depth
NRPN  73                       |     3    | Keyboard Level Scaling Right Depth
NRPN  74                       |     3    | Keyboard Level Scaling Left Curve
NRPN  75                       |     3    | Keyboard Level Scaling Right Curve
NRPN  76                       |     3    | Keyboard Rate Scaling
NRPN  77                       |     3    | Amp Mod Sensitivity
NRPN  78                       |     3    | Keyboard Velocity Sensitivity
NRPN  79                       |     3    | Operator Output Level
NRPN  80                       |   * 3    | Oscillator Mode
NRPN  81                       |   * 3    | Oscillator Frequency Coarse
NRPN  82                       |   * 3    | Oscillator Frequency Fine
NRPN  83                       |   * 3    | Oscillator Detune
NRPN  84                       |     2    | Envelope Generator Rate 1
NRPN  85                       |     2    | Envelope Generator Rate 2
NRPN  86                       |     2    | Envelope Generator Rate 3
NRPN  87                       |     2    | Envelope Generator Rate 4
NRPN  88                       |     2    | Envelope Generator Level 1
NRPN  89                       |     2    | Envelope Generator Level 2
NRPN  90                       |     2    | Envelope Generator Level 3
NRPN  91                       |     2    | Envelope Generator Level 4
NRPN  92                       |     2    | Keyboard Level Scaling Break Point
NRPN  93                       |     2    | Keyboard Level Scaling Left Depth
NRPN  94                       |     2    | Keyboard Level Scaling Right Depth
NRPN  95                       |     2    | Keyboard Level Scaling Left Curve
NRPN  96                       |     2    | Keyboard Level Scaling Right Curve
NRPN  97                       |     2    | Keyboard Rate Scaling
NRPN  98                       |     2    | Amp Mod Sensitivity
NRPN  99                       |     2    | Keyboard Velocity Sensitivity
NRPN 100                       |     2    | Operator Output Level
NRPN 101                       |   * 2    | Oscillator Mode
NRPN 102                       |   * 2    | Oscillator Frequency Coarse
NRPN 103                       |   * 2    | Oscillator Frequency Fine
NRPN 104                       |   * 2    | Oscillator Detune
NRPN 105                       |     1    | Envelope Generator Rate 1
NRPN 106                       |     1    | Envelope Generator Rate 2
NRPN 107                       |     1    | Envelope Generator Rate 3
NRPN 108                       |     1    | Envelope Generator Rate 4
NRPN 109                       |     1    | Envelope Generator Level 1
NRPN 110                       |     1    | Envelope Generator Level 2
NRPN 111                       |     1    | Envelope Generator Level 3
NRPN 112                       |     1    | Envelope Generator Level 4
NRPN 113                       |     1    | Keyboard Level Scaling Break Point
NRPN 114                       |     1    | Keyboard Level Scaling Left Depth
NRPN 115                       |     1    | Keyboard Level Scaling Right Depth
NRPN 116                       |     1    | Keyboard Level Scaling Left Curve
NRPN 117                       |     1    | Keyboard Level Scaling Right Curve
NRPN 118                       |     1    | Keyboard Rate Scaling
NRPN 119                       |     1    | Amp Mod Sensitivity
NRPN 120                       |     1    | Keyboard Velocity Sensitivity
NRPN 121                       |     1    | Operator Output Level
NRPN 122                       |   * 1    | Oscillator Mode
NRPN 123                       |   * 1    | Oscillator Frequency Coarse
NRPN 124                       |   * 1    | Oscillator Frequency Fine
NRPN 125                       |   * 1    | Oscillator Detune

Fixed Point vs. Floating Point Rendering
========================================
hexter can be compiled to do its sound rendering using either
fixed-point math or floating-point math.  The difference in sound
quality should not be audible, so the question of which type of math
to use becomes which type of math is faster. On many older
processors (e.g. PowerPC G4), the fixed-point math is substantially
faster. With newer processors, the speed of each type of math
depends on several factors, including the particular processor, the
compiler and the compiler options used.  It is often surprising
which is faster for a given configuration!

On fairly normal posix systems (like Linux or OS X), you can compile
a small test program, to test which type of math is the fastest on
your processor.  To do this, unpack the hexter tarball, cd into the
'fptest' directory, type 'make', then type './fptest'. After 30-60
seconds, you should see a summary of the test results.

By default, hexter is built to use fixed-point rendering. If your
test results say that floating-point is faster, then you can
configure hexter to use floating-point with the
'--enable-floating-point' configure option.

Here are some test results from a few machines. Percentages indicate
the speed relative to the faster mode.

Processor                              Fixed Point  Floating Point
-------------------------------------  -----------  --------------
PowerPC G4 800MHz                        faster          49.5%
PowerPC G4 1.07GHz                       faster          49.8%
Pentium III (Coppermine) 933MHz           95.2%         faster
Intel Core Duo 1.83GHz, OS X 10.4.9       92.9%         faster
Intel Core 2 Duo 2.4GHz, Linux 3.2        96.1%         faster *
Intel Core 2 Duo 2.4GHz, OS X 10.4.11     91.4%         faster *
Intel Core 2 Duo 2.4GHz, OS X 10.6.8     faster          61.5% *
Intel Core 2 Duo 2.5GHz, OS X 10.6.7     faster          62.0%
Intel Core i7, OS X 10.7                 faster          52.0%

* These three all come from the same machine!

Frequently Asked Questions
==========================
Q. The plugin seems to work fine, but the GUI never appears. Why?

A. Make sure the hostname of your machine is resolvable (if not, the
OSC messages can't be sent between host and GUI).  If your machine's
hostname is 'foo.bar.net', make sure you either have an entry for
'foo.bar.net' in /etc/hosts, or that your DNS server can resolve it.
Test this with e.g. 'ping foo.bar.net'. To test that the GUI itself
works, you can start it by itself (without a DSSI host) by giving it
the '-test' option, for example:

    $ <prefix>/lib/dssi/hexter/hexter_gtk -test

Resources:
==========
http://en.wikipedia.org/wiki/Yamaha_DX7
    The Wikipedia Yamaha DX7 page.

http://homepage.ntlworld.com/steve.sims/
    Steve Sims' site with lots of information, patches, and links.

http://homepages.abdn.ac.uk/mth192/pages/html/dx7.html
    Dave Benson's DX7 page, has much info as well as links to patch
    files.

http://www.synthzone.com/yamaha.htm
    The Synth Zone's collection of Yamaha links, which contains quite
    a few links to patch file collections.

