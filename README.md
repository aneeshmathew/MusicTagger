# Music Tagger

A single-file web app that finds song metadata on [MusicBrainz](https://musicbrainz.org/) and writes it into your MP3 files, in place. Open a folder, search, check the matches, then write the tags back. Works for music in any language.

## What it does

- Reads the music files in a folder you choose, including subfolders.
- Searches MusicBrainz for each song, using the file name (or the title you type).
- Fills in **Title, Artist, Album, Year, Composer, Lyricist and Cover Art**.
- Lets you edit every field before anything is written.
- Plays any song so you can confirm the match by ear.
- Writes the tags back into the original files in the folder you picked.

## Requirements

- **Chrome or Edge** (or another Chromium browser). Writing into the original folder needs the File System Access API. Other browsers can still search and edit, but they can only download tagged copies.
- An internet connection. The app calls MusicBrainz and the Cover Art Archive, and loads its tag-writing library and fonts from a CDN.
- **MP3 files** for tag writing. Other formats load and play but are skipped when writing.

## Getting started

1. Save `music_tagger.html` and open it in Chrome or Edge (double-click, or serve it locally).
2. Click **Choose folder**, or drag a folder onto the page, and allow read/write access.
3. Optionally set **Album year**, **Language** and **Title match** (see below).
4. Click **Find metadata**.
5. Review the table. Fix anything wrong, and use the play button on a row to listen.
6. Click **Write tags to folder**.

Back up your music before the first run. Writing replaces the ID3v2 tags in each file.

## How matching works

For each track the app searches MusicBrainz for recordings, then looks up the best candidate to get the release, composer and lyricist.

| App field | Comes from |
|---|---|
| Title | Recording title |
| Artist | Track artist |
| Album | Release title |
| Year | Release date |
| Composer | Work composer, otherwise the release artist (ignored if "Various Artists") |
| Lyricist | Work lyricist |
| Cover | Front cover (500 px) from the Cover Art Archive |

If MusicBrainz has no composer or lyricist for a song, the row shows **Matched, no credits**. You can type those in yourself.

### Filters that prevent wrong matches

- **Album year.** Accepts one year (`1998`) or a range (`1995-2000`). Only releases from those years are accepted. You can also type a year or range into a row's Year cell before re-searching that row.
- **Language.** The first 3 letters of a language code, for example `mal`, `tam`, `hin`, `eng`. This is a preference: releases marked with that language are tried first, but results in other languages are still used if nothing else matches. Leave it empty to turn it off.
- **Title match** (Strict, Normal, Loose). A result is rejected unless its title resembles the file name. Words are compared ignoring case and accents, with small spelling differences allowed. A song title fully inside a longer "movie - song" file name passes. Rejected rows show "Not found", and the status tooltip names the closest result. The check cannot compare Latin letters with another script, so those results are rejected under Strict and Normal and accepted only under Loose.

## The interface

| Control | What it does |
|---|---|
| **Find metadata** | Searches the selected tracks in the current view that are not yet matched. |
| **Include matched** | Makes Find metadata search matched and written tracks again. |
| **Retry failed (n)** | Searches again every track with an Error or Not found status. |
| **Write tags to folder (n)** | Writes tags for tracks that are ticked, in the current view, and Matched. |
| **Pause / Stop** | Appear while a job runs. Pause finishes the current track first. Stop ends the job. |
| **Rescan** | Re-reads the current folder. New files are added and deleted files removed. Existing matches and edits are kept. |
| **Change folder** | Loads a different folder. Asks first if you have matches that are not written yet. |
| **Count cards** | Click **Tracks**, **Matched**, **Written** or **Need attention** to filter the table. Find and Write only act on the rows you can see. |
| **Row refresh icon** | Searches that one track again, using the current text in its Title cell. |
| **Row play button** | Plays the file in the player bar above the table, next to the metadata that was found. |

### Status badges

| Badge | Meaning |
|---|---|
| Pending | Not searched yet. |
| Searching / Writing | In progress. |
| Matched | Metadata found, not written yet. |
| Written | Tags saved into the file. |
| Not found | No confident match, or the title check rejected every result. |
| Error | The request failed. Use Retry failed. |
| Skipped | Not written: not an MP3, or nothing to write. |

## Tags written

`TIT2` title, `TPE1` artist, `TALB` album, `TYER` year, `TCOM` composer, `TEXT` lyricist, `APIC` front cover. Multiple artists, composers or lyricists separated by commas, semicolons or `&` are saved as separate values. Existing ID3v2 tags in the file are replaced.

## Limits and notes

- **Speed.** MusicBrainz asks anonymous clients to stay under one request per second. The app spaces its requests, and each song needs two to five of them, so expect a few seconds per song. If MusicBrainz is busy, the app retries up to three times before giving up.
- **Data quality.** Many songs on MusicBrainz have no composer or lyricist, and not every release is marked with a language. The app can only write what MusicBrainz has.
- **File names are not changed.** The app only edits tags.
- **Unsupported browsers.** Without folder access, Write downloads a tagged copy of each file instead of changing the original.
- **Hidden files.** Hidden files and folders (names starting with a dot) are ignored.

## Supported file types

Loaded: mp3, flac, m4a, m4b, aac, wav, ogg, oga, opus, wma, aiff, ape, alac, wv. Playback depends on what your browser can decode. Tag writing: MP3 only.

## Troubleshooting

- **"MusicBrainz is busy" or "Could not reach MusicBrainz."** Wait a minute, then click **Retry failed**. Tracks not yet searched stay Pending and Find metadata picks them up.
- **Everything comes back Not found.** Try Title match set to Loose, clear the Album year, or edit the row's Title to the real song name and click the refresh icon.
- **Wrong year or wrong film.** Set Album year to the year you know, tick **Include matched**, and run Find metadata again.
- **Nothing to write.** Only ticked, Matched rows in the current filter view are written. Check the filter cards and the checkboxes.
- **Write permission was not granted.** Use **Change folder** and allow read/write access when the browser asks.

## Credits

Metadata from [MusicBrainz](https://musicbrainz.org/) and cover art from the [Cover Art Archive](https://coverartarchive.org/). Tag writing uses [browser-id3-writer](https://github.com/egoroof/browser-id3-writer).
