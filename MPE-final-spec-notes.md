# MPE final vs. draft notes

Comparison of RP35, the final 1.0 MPE spec (March 12 2018), with the widely implemented draft 1.25a (Dec 15 2015).


## TL;DR

Name change, Multidimensional Polyphonic Expression became MIDI Polyphonic Expression.

Zone creation (splits) simplified, changed, but *incompatible*. Probably saner, restricted to an Upper and Lower split.

The five dimensions of touch, which were *all 14-bit capable* in the draft spec, are **now reduced** to three 14-bit (glide/pitch, strike/note-on and lift/note-off velocity both with HR Velocty prefix) and **two 7-bit** (press and slide).

Other clarifications and corrections.

## 1.1 Background

Useful clarification that MPE mode is not the same as MIDI Mode 4 Omni Off/Mono. It extends MIDI Mode 3 ("Poly Mode").

## 1.2 Overview

Clarification, range of Pitch Bend now ±0 to ±96 (draft stated this to be ±1 to ±96). Corrects an obvious error in the draft spec, which later contradicted that range by saying "When the pitch bend range is set to zero".

Change, handling Poly Aftertouch previously "at the discretion of manufacturer" i.e. optional, now seems required. Still on Master channel only.

**Breaking Change,** CC70 (MSB) and CC102 (LSB) not mentioned for 14-bit aftertouch in this section. (*removed from the specification* **no 14-bit aftertouch any more**)

**Breaking Change,** CC106 for LSB of CC74 (slide, third axis) no longer mentioned in this section. (*removed from the specification* **no 14-bit slide any more**)

## 1.3 Terminology

New section.

## 2.1 MPE Mode Configuration

New name (MPE Configuration Message) still RPN 00 06. Also new spec, AMEI/MMA CA-034.

**Breaking change,** Details of the RPN are different, and incompatible with the earlier draft. Previous first example *Create a zone ranging from MIDI channels 2-8. Channel 1 will be the zone master channel* would now be invalid.

**Breaking Change,** splits now restricted to two: Lower and Upper Zones. Other splits, previously in draft spec, now invalid and **must be ignored**. Details of how splits map to a controller still left undefined.

**Breaking Change,** previously master channels were always to the left of the zone; now Lower is to the left (channel 1) and upper is to the right (channel 16), as demonstrated in example Two, and there are no other options.

Change, channel 1 can be a Member Channel for the Upper Zone if there is no Lower Zone.

Looks like partial overwrite/truncate is still there when a zone is created or modified.

Clarification? Examples now start with B0 79 00 or BF 79 00 **look into why**

Change, leaving MPE mode previously undefied or "preferred behavior", now MPE is deactivated by setting both upper and lower zones to use zero channels. But the behaviour is undefined (up to manufacturer) after you do that.

Change, power-on behavior was specified; but is now specified and  optional "at the manufacturer's discretion". Boo!

Clarification, stop all ongoing notes and reset all controls to "reasonable default values" on each channel entering or leaving MPE control.

## 2.2 Channel and Voice Assignment

New section.

Clarification, definition of "occupied channel".

Clarification, using MPE in Mode 4 (Lower Zone only) with mono devices. New note in a member channel will stop a previous note.

Clarification, switching between mode 3 and mode 4, definition of "basic channel" as lowest member channel.

## 2.3 Note Level Messages versus Zone Messages

(renamed from section Zone and Note Control)

Concept of Zone Scope is retained

*Table 1. How an MPE Device Uses Common MIDI Messages* clarifies and extends previous table *Assignment of commonly-used messages*.  Send and recieve treated separately.

**Breaking Change,** previously CC70-79 (MSB) and CC102-111 (LSB) supported per note (member channels) now restricted to **just CC 74** and **no LSB** for it.

Thus, **14 bit per-note slide is removed from the spec** Boo!!!!

Change, Program Change now valid only in Mode 4.

Change, Modes 1 and 2 should not be sent and must be ignored.

Change, Volume, Sustain/Damper, Modulation and "all other controller messages" not separately listed in the table were previously disallowed at note level; now "not recommended" to be sent and "cannot be expected to respond" on receipt. Means code should expect them to be sent, and silently ignore them.

Clarification, System Common, System Real-time, and System Exclusive  affect the entire system (was likely true before, now explicit).

## 2.4 Pitch Bend and Pitch Bend Sensitivity

Pitchbend sensitivity has to be sent to each of the member channels (one message per channel) but the eventual pitch bend values **must** be the same in each message and thus, the last one sent affects the **whole zone**. This means that controllers will *likely ignore* the requirement to send on all member channels, because just one message will have the same effect. But sending them all is recommended for compatibility.

Change, LSB of pitch bend sensitivity was previously *not used*, is now allowed but not recommended and optional (devices can "choose to respond").

### 2.4.1 Calibrated Pitch Instruments

New section

## 2.5 Channel Pressure and Polyphonic Key Pressure

Clarification, on receiving channel pressure on master channel, "combine meaningfully" on all sounding notes.

## 2.6 Third Dimension of Control

Clarification, on receiving CC74 on master channel, "combine meaningfully" on all sounding notes.

## 2.7 Further Dimensions of Control

New section saying other dimensions might be added later.

## 3.1 Use of One MPE Zone

Clarification, allowed to select between Upper and Lower zone if only able to respond to one.

Clarification, display the currently selected master and member channels for each zone.

## 3.2 Allocation of Notes to Member Channels

This is for sending; no advice for receivers allocating notes to voices.

Similar *handwavy prose* to draft spec. Allocate to channel with lowest number of active notes, except preferably re-use a channel that just turned off the same note number, except when you actually want the same note number to sound (with pitcbend) twice, except also favour the channel that would result in the smallest pitchbend for sounding notes on that channel, and then if you want, reduce pitchbend on that channel to just vibrato-level while multiple notes are sounding.

No interop with such vague and optional specification prose.

Clarification, must respond to note on/off messages on the master channel.

## 3.3 Maximizing Compatibility and Sound Quality Under MPE

New section, but incorporating some earlier material from the draft spec.

Clarification, note editing in DAWs and MIDI mergers can dynamically reassign notes to other channels if wanted.

Clarification, advice on correct way to send microtonal infrmation has been replaced by a worked examle, 3.3.1

Clarification, all per-note control messages must be tracked per-channel even when no notes are sounding.

Same advice that after note-off, subsequent pitchbend and other per-note controls *do not affect* the release portion of the note; expanded and clarified in 3.3.3 Pitch Bend.

Same advice to treat running-status NoteOn 0 as NoteOff 64, not 0.

### 3.3.4 Channel Pressure

*Handwaving,* channel pressure *must* be set to zero immediately before NoteOn or NoteOff, except when you don't want to for some reason.

### 3.3.5 Timbre Control

Clarification on absolute/initial-position CC74 vs. relative/initial64 CC74.

Section on **RPN handling** with recommendations by Lippold Haken for synchronous, glitch-free 14-bit handling, has been removed.

## 3.4 MPE Plugin Examples

Code snippets for VST and Audio Unit plugins to signal that they support MPE.

List of implementations removed.