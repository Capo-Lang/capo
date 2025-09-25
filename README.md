# Capo
Capo is a music notation programming language designed for fast, feature-rich, and customizable music entry.

Capo is currently just a language **definition**. CapoCompose is an object oriented implementation of Capo (also just a definition currently) that allows the creation of full scores.

This page will cover the definition of Capo. For its integration with CapoCompose, see the [CapoCompose](github.com/capo-lang/capocompose) page.

# Definition

## Notes
A note is represented by 3 distinct parts: a _prefix_, _value_, and _suffix_. For the purpose of this example, we will use the notes on a standard piano, but technically notes can have other values, like in the case of percussion notation.

### Prefixes
The prefix defines the value of a note. Valid prefixes are 1, 2, 4, 8, 16, and any other power of 2. These in turn represent whole, half, quarter, eighth, sixteenth, etc. notes. Prefixes can also be accompanied by any number of dots, like this:

```
8        = eighth note
2.       = dotted half note
4..      = double dotted quarter note
32.....  = quintuple dotted 32nd note
```

### Values
As stated before, values can technically be any letter, a-z. Capo is case-insensitive when it comes to notes, but we will be using lowercase.

### Suffixes
Suffixes modify the written note further. There are several types of suffixes and they can be written in any order, but we recommend writing them in this order:

#### Accidentals
Accidentals are written with `f` for flat, `s` for sharp, and `n` for natural. They can be combined for advanced accidental writing.
```
f = flat
s = sharp
n = natural
ff = double flat
ss = double sharp
fff = triple flat
... you get the idea
```

#### Octaves
One of the key implementations for speed in Capo is relative notes. More on this later, but notes can be written as relative or absolute. Relative octaves are based on the note that comes before, whereas absolute octaves are explicitly defined. By suffixing a note value with a number from 0 and above, you can specify the absolute octave of a note. By suffixing a note value with `'` or `,`, you can specify the note as higher or lower than the previous note, respectively. These relative symbols can be stacked to switch between multiple octaves at once.

#### Ties
By using the `t` suffix, you can add a tie to a note. This will tie the note to the next note if allowed.

#### Accents
The final use of suffixes is to add accents to a note. Common accents have dedicated symbols, and more advanced accents use variables. Combining certain accents leads to new accents, such as combining `.` (staccato) and `-` (tenuto) to make portato (mezzo-staccato). See the documentation for a full list of accents.

### Putting it all together
An example that uses all these elements together:

`4as5t.^`

This is equivalent to:

<img width="179" height="204" alt="image" src="https://github.com/user-attachments/assets/1a4d750a-bb54-4781-ac70-9bc2f30a718c" />

Notes are the basis for all the rest of notation with Capo.

## Rests
Rests are represented by a prefix and a value, and occasionally a suffix. The value is always `r`, and the prefix can be any of the prefixes that apply to notes. Rests might have suffixes like a fermata.

## Blocks
### Chords
A chord is multiple notes stacked together. In Capo, chords are represented by enclosing notes between `[` and `]`, such as `[4c5 4e5 4g5]`. To be a valid chord, all prefixes must be the same, and all accent suffixes must be the same. `[4c5. 4eb5. 4g4t.]` is a valid staccato chord, but `[4c5. 8e5. 4g5]` is not. This can get a little verbose to write, so Capo has a better way to deal with this. A `chord` is a subset of a `block`, and valid prefixes and suffixes can be moved outside of a block to apply to the whole block. `[4c5. 4e5. 4g5.]` can be rewritten as `4[c e g]5.` because all the notes share the same prefixes and suffixes.

### Blocks
A block is a group of notes, represented by enclosing notes between `{` and `}`. Unlike chords, blocks can contain a series of individual notes or chords. Blocks allow you to apply prefixes and suffixes to a group of notes easily. Take this C major scale: `8c4 8d 8e 8f 8g 8a 8b 8c`. If we wanted to write this in a simpler way, we could create a block: `8{c4 d e f g a b c}`.

#### Tuplets
Another use of blocks is for tuplets. Blocks allow you to use a special tuplet prefix to group notes in tuplets.

`3:2:4{c4 d e}` is a quarter note triplet pattern. `3:2:4` represents 3 notes in the space of two, with a quarter note value. A prefix of `7:11:8` would mean 7 eighth notes in the place of 11 eighth notes.

### Slurs
Another type of block is a slur. Enclose a series of notes between `(` and `)` to create a slur. As a block, slurs can be prefixed and suffixed, and because slurs can have multiple notes, they can also accept a tuplet prefix.

### Grace notes
Grace notes can be written by enclosing any number of notes between `<` and `>`. A slashed grace note can be achieved with double `<<` and `>>`. Like other blocks, grace notes can be prefixed and suffixed, and accept a tuplet prefix when more than one note is present.

```
<8a4> // grace note
<<8a4 8b4>> // slashed grace notes
```

## Bars
### Bar lines
The next important step in representing music is with bar lines. Bar lines bar lines are represented with a vertical pipe, `|`. As in written sheet music, bar lines help separate notes into bars. Several types of bar lines are available to use:

```
|   // normal bar line
||  // double bar line
|:  // repeat start
:|  // repeat
|]  // final bar
```

Advanced bar features such as multiple endings can be achieved through functions. More on those later. When using bars and blocks, blocks may span multiple bars.

### Layers
Sometimes, you want to write multiple parts in one staff. To do this, you can utilize layers. Layers are defined by separating groups of notes with a semicolon within a bar. Layers are defined in the order they are written:

```
| 4c4 4d 4e 4f ; 4c4 4b 4a 4g |]
```

### Repeated bars
When you have repeated sections of music, copying and pasting can get annoying. This is where the `%` symbol comes in handy. The `%` symbol is a shorthand to repeat the last measure exactly as written.

```
| 4{c d e f}5 | % |]
```

Or, if you want to repeat multiple bars, use multiple percents:

```
| 4{c d e f}5 | 4{e d 2c} | %% | %% |] // 6 bars of music
```

## Functions and variables

Functions and variables extend the capabilities of composition beyond notes on a staff. They allow you to add anything else to your music, from special symbols to dynamics to transposition to key changes.

### Variables
Variables are a drop in replacement for any prefix, value, or suffix. Variable definitions are not in the scope of Capo and must be defined in CapoCompose, but are used for symbols beyond what the basic keystrokes can provide. Variables are defined by enclosing a variable name in `${}`. Here's an example that uses a variable prefix, value and suffix:

```
${maxima}${snare}${fermata}
```

This example defines a snare hit with a value of a [maxima](https://en.wikipedia.org/wiki/Maxima_(music)) and a fermata. Variables can be used alongside standard accents, notes, and durations.

### Functions
There are two types of functions in Capo, staff and score functions. Staff functions apply only to the staff they are used in. Score functions apply to all staves at that point in the score. One example of a function is dynamics. Dynamics can be set at the staff or score level, depending on how you want to write them.

To call a staff function, prefix it with `$`. To call a score function, prefix it with `@`. A staff dynamics function call may look like `$dynamic("mf")`, while a score dynamics function call may look like `@dynamic("pp")`.

Several highly used functions are:

```
@key("C")
@time("4/4")
@tempo(120, 4)
$clef("t")
$cresc()
$decresc()
$dynamic("sfz")
$ending(1)
$transpose(a4 b c d, 3)
```

Functions can take 3 types of inputs: strings, numbers, and music. Strings are always double-quoted, and numbers can be integers or floating point, positive or negative. Music is any valid Capo syntax, including nesting functions within functions. The exception here is using the `,` (comma) character. Any note that uses this character must be passed in a block to prevent the function from interpreting it as two separate function parameters.

One thing to note is that functions and variable definitions are determined by the program processing Capo. In CapoCompose, functions and variables are defined or imported before use, therefore the specific use of functions and variables in the examples provided here may differ from actual implementations.

## Assumptions and omissions
There are several ways to shorthand writing in Capo. Some of these we have already encountered, such as using blocks to apply prefixes and suffixes to groups of note values, or `%` to repeat entire bars.

There are several other ways to simplify writing music in Capo, and most have to do with parameters set by score functions.

`@key()`: After setting a key, accidentals can be omitted from notes. Defining an accidental that is in the key will not change the resulting note, but accidentals that are in the key can be safely omitted. This means that a natural note in a key with a sharp **must be explicitly set**.

Similarly, setting an accidental on a note in a bar will automatically set that accidental for all instances of that note for the rest of the bar.

`@time()`: After setting the time signature, omitting a duration prefix will assume the base duration of the time signature. For example, setting the time signature to 4/4 will make the note `a5` a quarter note, while setting the time signature to 6/8 will make `a5` an eighth note. This applies to rests as well, which can be written as just `r`.

Rests can also omit the `r` value and provide just a duration. `4` is a quarter rest, `8.` is a dotted eighth rest.

When a time signature is set, each bar has an expected amount of beats. Adding extra beats to a measure is allowed, but writing fewer beats than expected will assume the measure is filled with rests. The exception here is when two barlines are separated by only white space or no notes or rests. This will be treated as a single bar line. This allows you to logically separate lines like this:

```
| 4{c4 d e f | g a b c} |
| 4{ c b a g | f e d c} |]
```

One helpful feature of blocks is overriding note attributes. Any attribute (prefix or suffix) defined on a note inside a block will take priority over an attribute outside the block. This means that if you define a block with `4{}` but then put an explicit eighth note in the block, the eighth note will take priority.

`$clef()`: When a clef is set and a relative note is written before any absolute note, the octave where that note is most on the staff is assumed. It is recommended to not do this, and instead clearly define an absolute octave at a clef change.

## Lyrics
Lyrics are achieved by creating any number of quoted strings at any point (excluding quoted strings passed as function parameters). This allows you to group your Capo code logically by section and include lyrics wherever makes sense visually. The position of lyrics relative to the music does not matter, the lyrics are aligned starting with the first note.

Lyric words are separated by any whitespace, and syllables are separated by an em dash and no whitespace. Lyrics sustained under a note are achieved with a single underscore for each sustained note, and skipped notes are represented by one or more em dashes surrounded by white space.

```
| 4{c4 d e f | g a b c } |
"These are ly-rics to this song_"
| 4{c b a g | f e d c } |
"I will sing_ all day - long"
```

### Lyric layers
Multiple verses in lyrics can be achieved in a similar way to layers on a staff. Within a quoted lyric string, type a semicolon to end the current layer and begin the next. Layers all begin at the same point within one block of lyrics, and the next block begins at the furthest point of any layer in the previous block.

```
"lay-er one ly-rics ; lay-er two ly-rics"
"back to lay-er one ; then lay-er two now"
```

If any of the special characters need to be included in a lyric, preceed them with a backslash (ex. `lyr-\-ic` will print out "lyr" and "-ic"). Valid escaped characters are `\-`, `\_`, `\;` and `\\`. All other non-whitespace characters will print as-is.

## Comments
Comments can be made using `//` outside of a quoted string and are terminated at the end of a line.

## Examples

### Twinkle twinkle little star
```
@key("C")
@time("4/4")
@tempo(120, 4)
$clef("t")

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

### Blue Skies
```
@tempo(128, 4, "Medium swing")
@swing("medium")
@time("4/4")
@key("Cm")
$clef("t")


@section("A")
|: 2c5 2g't | 2g 8{f e} f | 1gt | 2g 8{f e} f |
"Blue skies_ smi-ling at me_ noth-ing but ;
Blue-birds_ sing-ing a song,_ noth-ing but"

| 2g  2b,t | 2b 8b c 8et | @ending(1) 1et | 1e :| @ending(2) %% |
"blue skies_ do I see.__ -- ;
blue-birds_ all day long. -- __"

@section("B")
| 8{e f g a} 2b | 8{cf b} a 2b | 8{a g} f 2g | 8{f e} d 2e |
"Nev-er saw the sun shin-ing so bright, nev-er saw things go-ing so right."

| %%%% |
"No-ti-cing the days hur-ry-ing by, when you're in love, my how they fly"

@section("A")
|: 2c5 2g't | 2g 8f 8e f   | 1gt | 2g 8{f e} f |
"Blue days,_ all of them gone,_ noth-ing but"

|  2g  2b,t | 2b 8b c  8et | 1et | 1e          |]
"blue skies_ from now on.__"
```

More examples to come!
