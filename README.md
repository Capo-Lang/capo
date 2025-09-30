# Capo

**Version:** 0.1 (draft)

**Date:** 9/26/2025
## Overview
Capo is a modern music notation programming language designed for fast, feature-rich, and customizable music entry. It enables musicians to write musical notation using text-based syntax that is both expressive and efficient.

Capo focuses on writing individual musical lines with powerful shorthand notation. For multi-instrument compositions and full scores, see [CapoCompose](http://github.com/capo-lang/capocompose), which extends Capo with document structure and ensemble management.
### Design Philosophy
Capo is designed around several key principles:
- **Speed -** Minimize keystrokes through intelligent defaults and shorthand notation
- **Readability -** Code should be readable by both humans and computers
- **Flexibility -** Support for standard notation plus extended techniques and custom symbols
- **Musical logic -** Syntax follows musical thinking patterns rather than programming conventions
## Core concepts
### Note Anatomy
Every note in Capo consists of three parts: \[prefix]\[value]\[suffix]
- **Prefix -** Duration (4, 8, 16, etc.) with optional dots
- **Value -** Pitch name (a, b, c, d, e, f, g) or special values like `r` for rests
- **Suffix -** Modifiers like octave, accidentals, ties, or articulations

_Example:_ `4cs5t.^` breaks down as:
- `4` = quarter note (prefix)
- `c` = pitch C (value)
- `s5t.>` = sharp, octave 5, tie, staccato, accent (suffixes)
### Note Components
#### Duration Prefixes
Prefixes specify note duration using powers of 2:
```capo
1       // whole note
2       // half note
4       // quarter note
8       // eighth note
16      // sixteenth note
32      // thirty-second note
64      // sixty-fourth note
```
**Dotted Notes:** Add dots after the number to extend duration:
```capo
4.      // dotted quarter note
2..     // double dotted half note
8...    // triple dotted eighth note
```
#### Pitch Values
Pitch names use letters a through g (case-insensitive):
```capo
a b c d e f g   // standard pitch names
r               // rest
```
**Note:** While pitch names are typically musical letters, Capo technically supports any letter a-z for specialized notation systems (like percussion).
#### Suffixes
Suffixes modify notes and can be combined in any order. The recommended order for readability is:
\[accidentals]\[octave]\[ties]\[articulations]
##### Accidentals
```capo
s       // sharp (♯)
f       // flat (♭)
n       // natural (♮)
ss      // double sharp
ff      // double flat
```
##### Octaves
**Absolute Octaves:**
```capo
c4       // middle C (octave 4)
a0       // lowest A on piano
c8       // highest C on piano
```
**Relative Octaves**
```capo
c4 d e f        // d, e, f choose closest octave (c4 d4 e4 f4)
c4 f a c        // octaves are relative to the previous note (c4 f4 a4 c5)
c4 g'           // g explicitly above c4 (g4)
c4 f,           // f explicitly below c4 (f3)
c4 g''          // g explicitly above c4, plus one more octave (g5)
c4 f,,          // f explicitly below c4, plus one more octave (f2)

// Without explicit direction, chooses closest
c4 fs           // f# chooses closest = f#4 (tritone goes up)
c4 b            // b chooses closest = b3 (down a semitone)
```
##### Ties
```capo
4ct     // quarter C with tie to next note
```
##### Articulations
Common articulations have single-character symbols:
```capo
.      // staccato
-      // tenuto
>      // accent
^      // marcato
!      // sccatissimo
*      // fermata
~      // trill
=      // tremolo
```
**Note:** this is not a comprehensive list of available articulations. Articulations will be added as the language develops.

**Combined Articulations:** Some combinations create new meanings
```capo
.-      // portato (staccato + tenuto)
```
**Complex Articulations:** Use variables for extended techniques
```capo
4c{pizz}  // pizzicato
4c{gliss} // glissando
```
### Complete Note Examples
```capo
4c           // quarter note C
8gs4         // eighth note G-sharp, octave 4  
2bf5t        // half note B-flat, octave 5, tied
4as'.->      // quarter A-sharp, relative octave up, portato, accent
16r          // sixteenth rest
{whole}{snare}{fermata}  // whole note snare hit with fermata
```
### Rests
Rests use the same prefix system as notes with `r` as the value:
```capo
4r      // quarter rest
2.r     // dotted half rest
8r      // eighth rest
```

**Shorthand Rests:** When time signature is set, you can omit the `r`:
```capo
4       // quarter rest (when time signature is active)
8.      // dotted eighth rest
```
## Grouping
### Blocks
Enclose sequential notes in curly braces to apply common attributes:
```capo
8{c4 d e f g a b c}     // C major scale in eighth notes
4{c e g}5               // three quarter notes, octave 5, staccato
```
#### Tuplets
Tuplet notation is a unique prefix that can be applied to blocks:
```capo
3:2:4{c4 d e}           // triplet: 3 quarter notes in the space of 2
5:4:8{c d e f g}        // 5 eighths in the space of 4
7:8:16{c d e f g a b}   // 7 sixteenths in the space of 8
```
Format: `notes:in_the_space_of:base_duration{music}`
### Chords
Enclose simultaneous notes in square brackets to create chords:
```capo
[4c4 4e 4g]             // C major triad
4[c e g]4               // same chord using block notation
4.[c e g]4>             // dotted chord with accent
```
**Chord rules:**
- All notes must have the same duration prefix
- All notes must have the same articulations
- Prefixes and suffixes can be factored outside the brackets
### Slurs
Enclose phrases in parentheses to create slurs:
```capo
(4c4 4d 4e 4f)          // slurred phrase
4(c d e f)4             // same phrase using block notation
3:2:8(c4 d e)           // slurred triplet
```
Slurs, like blocks, can be prefixed with tuplet notation.
## Measures and Structure
### Bar Lines
Bar lines separate musical content into measures:
```capo
|        // bar line
||       // double bar line  
|:       // repeat start
:|       // repeat end
|]       // final bar line
```
### Layers
Write multiple voices on one staff using semicolons:
```capo
| 4c4 4d 4e 4f ; 4c2 4d 4e 4f |    // two voices in one measure
```
The order of layers is maintained across measures.
### Repeated Measures
Use percent symbols to repeat previous measures:
```capo
| 4c4 4d 4e 4f |       // original measure
| % |                  // repeat last measure  
| %% |                 // repeat last two measures
```
### Multi-Measure Rests
Surround a number in square brackets to create multi-measure rests:
```capo
| 4c4 4d 4e 4f |
| [2] |                // 2 measures of rest
| 4g 4a 4b 4c |
```
### Measure grouping
Blocks can span multiple measures:
```capo
| 4{c4 d e f | g a b c} |     // block spans bar line
| 4{c b a g | f e d c} |]     // logical grouping
```
For visual clarity, bar lines can be repeated after whitespace.
## Functions and Variables
### Variables
Variables substitute custom symbols using brace notation:
```capo
{staccato}              // articulation variable
{snare}                 // percussion instrument variable  
{fermata}               // symbol variable
4a{staccato}^           // quarter A with staccato and accent
{whole}{kick}{accent}   // whole note kick drum with accent
```
**Variable Context:** Variables can replace any part of a note (prefix, value, or suffix) and are defined in the host environment (like CapoCompose).
### Functions
Functions provide dynamic control and advanced notation features:
```capo
key("C")                // set key signature
time("4/4")             // set time signature  
tempo(120, 4)           // set tempo (120 BPM, quarter note)
clef("treble")          // set clef
dynamic("f")            // set dynamic level
crescendo()             // start crescendo
transpose(c4 d e f, 12) // transpose up one octave
grace(g3 a)
```
**Function Scope:** Functions can apply to different aspects of a score and, like variables, are defined in the host environment (CapoCompose):
- **Score functions** (key, tempo, time) affect the entire piece
- **Staff functions** (clef, dynamic, articulations) affect only the current staff
- **Local functions** (transpose, grace) affect specific passages.
**Function Parameters:** Functions accept three parameter types:
- **Strings -** `"trebel"`, `"4/4"`, `"mp"`
- **Numbers -** 120, 4, -3, 2.5
- **Music -** Any valid Capo notation (use blocks for comma-containing music) **Note:** Because functions are valid Capo notation, functions can be nested.
## Smart Defaults and Shortcuts
Capo includes several intelligent features to reduce typing:
### Key Signature Awareness
After setting a key signature, accidentals follow musical rules:
```capo
key("G")               // G major (F#)
| 4g4 4a 4b 4c |      
| 4d 4e 4f 4g |        // f is automatically F#
| 4d 4e 4fn 4g |       // fn explicitly specifies F natural
```
**Accidental Rules:**
- Key signature accidentals are applied automatically
- Explicit accidentals override key signature
- Accidentals persist through the measure
### Time Signature Shortcuts
After setting a time signature, default note values match the base time unit:
```capo
time("4/4")
| c4 d e f |           // assumes quarter notes

time("6/8")
| c d e f g a |        // assumes eighth notes
```
#### Incomplete Measures
Measures with fewer beats than expected are automatically filled with rests:
```capo
time("4/4")
| 2c4 |           // becomes | 2c4 2r |
| 4c |           // becomes | 4c 4r 2r |
```
### Relative Octaves with Clef
When a clef is set, the first relative note chooses the most appropriate octave:
```
clef("treble")
| c d e f |             // c automatically becomes c4

clef("bass")  
| c d e f |             // c automatically becomes c3
```
**Note:** Clef default octaves are set in the host environment (CapoCompose). Custom clefs can be defined with custom default octaves.
### Block Overrides
Individual notes within blocks can override block attributes:
```capo
4{c4 d e 8f}            // f becomes eighth note despite block prefix
8{c4 dn e f}s           // equivalent to 8cs4 8d 8es 8fs
```
## Lyrics
### Basic Lyrics
Add lyrics using quoted strings anywhere in the music:
```capo
| 4c4 4c 4g' 4g | 4a 4a 2g | 
"Twin-kle twin-kle lit-tle star" 

| f f e e | d d 2c | 
"How I won-der what you are"
```
**Lyric Rules:**
- Position relative to music doesn't matter. Alignment is automatic
- Words are separated by whitespace
- Syllables are separated by hyphens with no whitespace (`lyr-ics`)
- Sustained notes use underscores (`held_`)
- Skipped notes use dashes surrounded by whitespace (` - `)
- Use multiple underscores to hold a lyric under multiple notes.
- Rests are automatically skipped by lyrics.
### Advanced Lyric Features
#### Multiple Verses
Use semicolons to separate verse layers:
```capo
| 2c5 2g't | 2g 8{f e} f | 1gt | 2g 8{f e} f | 
"Blue skies_ smi-ling at me_ noth-ing but ; 
Blue-birds_ sing-ing a song,_ noth-ing but"
```
**Multiple Lyrics Rules:**
- If only one layer of music is written, all lyrics align to that layer
- If multiple layers of music are written, sequential verses align to sequential layers
- If more verses exist than layers, overflow lyrics align to the first layer
#### Special Characters
Escape special characters with backslashes:
```capo
"lyr-\-ics"               // prints "lyr-ics" (literal hyphen)
"word\_with\_underscores" // prints "word_with_underscores"
"verse\; one"             // prints "verse; one" (literal semicolon)
```
## Comments
Use `//` for single-line comments:
```capo
// This is a comment
| 4c4 4d 4e 4f |        // end-of-line comment
```

Enclose multi-line or in-line comments between `/*` and `*/`:
```capo
/* This is a 
multi-line comment */

| 4c4 4d /* internal comment */ 4e 4f |
```

Comments cannot appear inside quoted strings.
## Complete Examples
### Twinkle, Twinkle, Little Star
```capo
key("C")
time("4/4")
tempo(120, 4)
clef("trebel")

| 4{c4 c g' g | a a 2g} |
"Twin-kle twin-kle lit-tle star"

| 4{f f e e | d d 2c} |
"How I won-der what you are"

| 4{g' g f f | e e 2d} |
"Up a-bove the world so high"

| 4{g g f f | e e 2d} |
"Like a dia-mond in the sky"

| 4{c c g' g | a a 2g} |
"Twin-kle twin-kle lit-tle star"

| 4{f f e e | d d 2c} |]
"How I won-der what you are"
```
### Blue Skies (Jazz Standard)
```capo
tempo(128, 4, "Medium swing")
swing("medium")
time("4/4")
key("Cm")
clef("trebel")


section("A")
|: 2c5 2g't | 2g 8{f e} f | 1gt | 2g 8{f e} f |
"Blue skies_ smi-ling at me_ noth-ing but ;
Blue-birds_ sing-ing a song,_ noth-ing but"

| 2g  2b,t | 2b 8b c 8et | ending(1) 1et | 1e :| ending(2) %% |
"blue skies_ do I see.__ -- ;
blue-birds_ all day long. -- __"

section("B")
| 8{e f g a} 2b | 8{cf b} a 2b | 8{a g} f 2g | 8{f e} d 2e |
"Nev-er saw the sun shin-ing so bright, nev-er saw things go-ing so right."

| %%%% |
"No-ti-cing the days hur-ry-ing by, when you're in love, my how they fly"

section("A")
|: 2c5 2g't | 2g 8f 8e f   | 1gt | 2g 8{f e} f |
"Blue days,_ all of them gone,_ noth-ing but"

|  2g  2b,t | 2b 8b c  8et | 1et | 1e          |]
"blue skies_ from now on.__"
```
## CapoCompose
Capo serves as the notation engine within [CapoCompose](github.com/capo-lang/capocompose) documents. All Capo syntax works unchanged within CapoCompose blocks:

```capo
// CapoCompose document setup
import key, clef from score
import staccato from accents

doc.title "My Song"
inst piano { 
    staff rh {} 
    staff lh {} 
    name "Piano"
}

score {
    piano
}

// Capo notation is used to write music
piano.rh {
    key("C") clef("treble")
    | 4c4 4d 4e 4f |             // Standard Capo syntax
    | {staccato} 8{g a b c5} |   // Variables and blocks
}
```
---
_This specification is a living document and will be updated as the language evolves._
