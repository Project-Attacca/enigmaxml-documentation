# EnigmaXML Format

## Table of Contents
- [Overview](#overview)
- [Root Element](#root-element)
- [Node Structure](#node-structure)
- [Node Definitions](#node-definitions)
  - [mappings](#mappings)
  - [header](#header)
  - [options](#options)
  - [others](#others)
  - [details](#details)
  - [entries](#entries)
  - [texts](#texts)

## Overview
EnigmaXML is the internal proprietary format that Finale uses to store score data.

## Root Element
The root element is `finale`. This element encapsulates the entire XML structure and includes two attributes.

### Attributes
- **version** (string): Indicates the version of Finale that created the document.
- **xmlns** (string): Specifies the XML namespace for the document. The value appears always to be `"http://www.makemusic.com/2012/finale"`.

## Node Structure

The highest level nodes correspond to each of the major sections of an Enigma file.
Items are identified with an Enigma concept called a `cmper`. Cmpers are 16-bit values internally in Finale. Some are signed and others are not. Some are always sequential and some are not. There is a pool of items called `others` that contain a single cmper and a pool of items called `details` that contain two cmpers, `cmper1` and `cmper2`. Cmpers are usually 1-based, but there are exceptions.

Some data types allow for multiple instances of the same cmper or pair of cmpers. The separate instances are indentified with 0-based incis that are always sequential. These are basically array types.

Additionally, the `text` pool calls its cmpers `numbers` but they function the same as cmpers.

The `entry` pool specifies the notes, chords, and rests. Each entry is identified by an `entnum`. Internally within Finale they are signed 32-bit values. If a document is so big that it would exceed `INT_MAX` entries, it becomes corrupted. However, a file is much more likely to run out of frames (which use a 16-bit cmper) before it runs out of entry numbers. Running out of frames also corrupts the file.

### Example
```xml
<finale version="27.4" xmlns="http://www.makemusic.com/2012/finale">
    <mappings/>
    <header/>
    <options/>
    <others/>
    <details/>
    <entries/>
    <texts/>
</finale>
```

## Node Definitions

### mappings

Documentation needed.

### header

Contains metadata about the file such as encoding information, locale information, creation and modification dates, and the Finale version that created it.

### options

The nodes under `options` correspond to the Document Options. The classes of the PDK Framework ending in `Prefs` also represent the Document Options. Unfortunately, there is not always a clear mapping between `options` and the `Prefs` classes.

### others

This is the pool of data structures with a single `cmper`.

- **frameSpec** Cmpers are 1-based and there may be gaps. Each occurrence defines the entries in a layer of music in a measure on a staff. To get to the frame, look at the `gfhold` node of the staff/measure combo in the `details` pool. Note that the `startEntry` and `endEntry` values are entry numbers and point to start and end positions in a doubly-linked list. You cannot do math on the entry numbers and know anything about how many entries are in a frame.
- **instUsed** Cmper is 0 for Score View and 1-n for each `staffSystemSpec`. (There are some other reserved instument lists with large-valued cmpers over 65000.) Each `inci` element specifies the next staff for that instrument list. The `inst` element specifies the `staffSpec` cmper. See [`FCSystemStaves`](https://pdk.finalelua.com/class_f_c_system_staves.html).
- **measSpec** Cmpers are 1-based and sequential. Each instance defines the next measure of music. See [`FCMeasure`](https://pdk.finalelua.com/class_f_c_measure.html).
- **staffSpec** Cmpers are 1-based and may contains gaps. Each ocurrence defines a staff. The cmpers do not specifiy the score order. They typically follow the order that staves were added to the document. See [`FCStaff`](https://pdk.finalelua.com/class_f_c_measure.html).
- **staffSystemSpec** Cmpers are 1-based and sequential. Each instance defines the next staff system. Each system has its own list of staves (`instUsed` array). See [`FCStaffSystem`](https://pdk.finalelua.com/class_f_c_staff_system.html).

### details

This is the pool of data structures with two cmpers, `cmper1` and `cmper2`.

- **gfhold** The `cmper1` value identifies the `staffSpec`. `cmper2` identifies the `measSpec`. See [`FCCellFrameHold`](https://pdk.finalelua.com/class_f_c_cell_frame_hold.html). Every staff/measure combo that contains either music or a clef change has one of these. Completely empty measures may omit them. The `frame1` thru `frame4` subnodes correspond to layers 1 thru 4.

### entries

This is the pool of entries. Each entry may either be a rest or contain 1-15 notes. If a rest is non-floating, it will also contain a "note" that specifies its position on the staff. Entries form a series of doubly-linked lists. If a `prev` or `next` attribute is `0`, that means the list ends there. The [`FCNoteEntry`](https://pdk.finalelua.com/class_f_c_note_entry.html) class more-or-less corresponds to an entry, though Finale plugins access them differently, and Finale provides some calculated values to plugins that are not in the XML.

### texts

This pool corresponds to the [`FCRawText`](https://pdk.finalelua.com/class_f_c_raw_text.html) class. There are several different pools of raw text, each with its own `number` sequence. The sequence is 1-based and there can be gaps.
