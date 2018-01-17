# Carnegie RFP Samples

1. OHMS/ the idiosyncratic xml format used by the OHMS project
2. SRT/ https://matroska.org/technical/specs/subtitles/srt.html
3. WebAnno/ the W3C Web Annotation recommendation http://w3c.github.io/web-annotation/
4. WebVTT/ https://w3c.github.io/webvtt/


SRT is the simplest, least flexible, and requires the most presumption regarding format for metadata.

Not covered are YouTube's preferred format:
 SCC http://www.theneitherworld.com/mcpoodle/SCC_TOOLS/DOCS/SCC_FORMAT.HTML a binary format

Or iTunes':
  TTML https://www.w3.org/TR/ttml1/ (iTT subset documented elsewhere)

## Background

The OHMS system produces two sets of data that are **synchronized** (correlated by time offsets) to a recorded interview, packaged together in a single XML file. These data are a transcript and an index.

### Synchronized Transcripts

OHMS XML describes a synchronized transcript via content in two elements:

`/ROOT/record/sync` contains a block of data in an idiosyncratic format representing a series of time offsets in seconds from the beginning of the interview and a number of lines in the transcript offset from the previous encoded time.

`/ROOT/record/transcript` contains the transcript encoded in plain text, with significant line breaks.

The transcript may exist without the `sync` information; however, the `sync` information can only be understood with reference to the plain text transcript and the recording.

### Synchronized Index

OHMS XML describes a synchronized index via a series of `point` elements under `/ROOT/record/index`. Each `point` element contains a `time` element indicating the offset, in seconds, at which the interview fragment described by the parent `/ROOT/record/index/point` begins. It does not indicate the length of the interview fragment. Other, sibling elements to `/ROOT/record/index/point/time` contain descriptive metadata for the fragment. The content of each `/ROOT/record/index/point` is referred to as an **index document**.

The `/ROOT/record/index` is independent from the `/ROOT/record/transcript` and the `/ROOT/record/sync`, and is present without them in some OHMS interviews.

## Goals

OHMS is a full-stack tool that is not designed to be incorporated into other systems for managing digital media and their descriptions. The CARSYNC project aims to produce a Javascript synchronization tool that allows the creation of functionally similar synchronized data to OHMS's `sync`, `transcript`, and `index` data, and can be re-used in other contexts. Of principal interest to Columbia is its re-use in a local **workflow manager**, which will identify the digital media and plain text transcript associated with an interview to the CARSYNC synchronizer.

### Target Serialization

The reproduction of OHMS-compatible XML is desirable, but the first order concern for the project is the production of functionally similar data in standard formats. Both transcript and index synchronization in OHMS are fundamentally the association of time offsets with related metadata, and it is desirable to use the same mechanism for describing the time associations of both types of synchronized data.

The candidate formats were **SRT**, **WebVTT**, and **WebAnno** (the W3C WebAnnotation standard). *SRT* is popular but not formally specified. *WebVTT* has a specification, broad adoption, and some precedent for including tagged metadata in the caption content. *WebAnno* has relatively little adoption, but it is well specified and compatible with some related tooling in the sector (cf the IIIF-AV projects).

Of the candidate formats, only *WebAnno* has an existing mechanism for including both a transcript and an index synchronization in a single serialized document; using *SRT* or *WebVTT* is understood to imply the separate management of the synchronized transcript and the synchronized index. We have begun defining a document format for the *workflow manager* to communicate the writeable URLs for the index and transcript represented as separate serialized data in another document.

There is existing work in playing synchronized transcripts back in AblePlayer (http://digital.wustl.edu/eyesontheprize/browse.html) using *WebVTT*, and Columbia currently uses AblePlayer, so *WebVTT* serialization is the highest priority and our use cases for the player and synchronizer are written with *WebVTT* in mind.

There is prototype work using *WebAnno* in the IIIF-AV community:
- player: https://avalonmediasystem.github.io/avalon-iiif-player/build/
- manifest: https://dlib.indiana.edu/iiif_av/lunchroom_manners/lunchroom_manners_v5.json

But these players are still under development and so, like OHMS, export compatibility with this format is desirable but not a priority.