# Scriptorai Open Esoteric Library — data

The texts of the nine books the [Scriptorai Open Esoteric Library](https://scriptorai.org) publishes. For each page you get its transcription line by line, an English translation of each line, the page's running heads, footnotes, marginal notes and page numbers, and a short summary of the page. Every line has a permanent id, so a citation keeps pointing at the same printed line after the reading is corrected.

The files are JSON and need no software to read: 1,754 pages and 49,896 lines in all.

## The books

| Address | Book | Pages | Lines |
|---|---|---:|---:|
| `ccag/1` | *Catalogus Codicum Astrologorum Graecorum* I, Codices Florentini (Brussels, 1898) | 184 | 6,377 |
| `ccag/2` | *Catalogus Codicum Astrologorum Graecorum* II, Codices Veneti (Brussels, 1900) | 226 | 8,170 |
| `ccag/3` | *Catalogus Codicum Astrologorum Graecorum* III, Codices Mediolanenses (Brussels, 1901) | 59 | 1,851 |
| `ccag/4` | *Catalogus Codicum Astrologorum Graecorum* IV, Codices Italici (Brussels, 1903) | 193 | 5,983 |
| `ccag/5.1` | *Catalogus Codicum Astrologorum Graecorum* V.1, Codices Romani, pars prior (Brussels, 1904) | 257 | 8,319 |
| `tanbihat-al-munajjimin` | Gunābādī, *Tanbīhāt al-munajjimīn*, a Persian lithograph (1284 AH / 1868) | 298 | 6,927 |
| `gunabadi-sharh-i-bist-bab-columbia` | Gunābādī, *Sharḥ-i Bīst bāb*, with an anonymous manual of judicial astrology; a Persian manuscript, Columbia MS Or 26 (1244 AH / 1828) | 271 | 4,975 |
| `mashaallah-libri-tres` | Māshāʾallāh, *Libri tres*, in Latin (Nuremberg, 1549) | 139 | 4,041 |
| `albohali-de-iudiciis-nativitatum` | al-Khayyāṭ (Albohali), *De iudiciis nativitatum*, in Latin (Nuremberg, 1549) | 127 | 3,253 |

A page counts if it carries any text of the book. Blank leaves and covers have no file, and neither do pages that carry only a library's or a digitizer's marks or a modern notice bound into the scan.

The transcriptions, translations and summaries were produced with hemiplon, which reads each scanned page in several independent language-model passes, reconciles them into one reading, and translates it.

## Files

```
catalogue.json                            one entry per book
data/pages/<address>/<sheet>.json         one file per page
data/ledgers/<key>.jsonl                  every line id a book has had
data/index/lines/<key>/<c>.json           where each line id stands now
```

`<key>` is the address with its slash turned into a hyphen: `ccag-1`, `ccag-5.1`, `tanbihat-al-munajjimin`.

### `catalogue.json`

Each book's bibliography, the scan it was read from, its rights, and how its scan sheets map to printed page numbers. The CCAG volumes sit under `titles.ccag.volumes`; each other book is `titles.<address>.edition`.

| Field | What it holds |
|---|---|
| `title`, `volume_label` | The book's title; for a volume of a series, its number as the series prints it (`V.1`). |
| `bibliography.author`, `.editor` | As the book names them, or `null`. |
| `bibliography.published` | The imprint: place, printer or publisher, year. `null` for the manuscript. |
| `bibliography.date` | When it was printed or copied. |
| `bibliography.language` | The language code the text is compared under: `grc` for the CCAG volumes (Latin and Greek), `la`, `fa`. |
| `bibliography.language_note` | The languages of the pages, in words. |
| `source.institution`, `source.item` | Who holds the scan the text was read from, and its public address. |
| `source.pages` | How many scanned pages of the book the reading covers, blanks included. |
| `rights.status` | `public-domain` for all nine books. |
| `rights.statement` | What the scan itself says about its terms, quoted, or `null` where it says nothing. |
| `rights.basis` | Why the text is in the public domain. |
| `publish.state` | `public` for all nine books. |
| `pagination` | Runs of scan sheets and the printed number each run starts from. `{"sheets": [15, 199], "numbering": "arabic", "printed_from": 1}` means sheet 15 bears page 1, sheet 16 page 2, and so on to sheet 199. |
| `leaves` | Single sheets that carry a printed number of their own, such as a leaf inserted from a second copy (`0005d`). |
| `files.pages`, `files.ledger` | Where the book's page files and ledger are. |

### Page files

One file per scanned page, named by its sheet: the page's position in the scan in four digits, with a letter after it for a leaf inserted later (`0005a`). The page number the site shows is the sheet without its leading zeros, so page 112 of CCAG I on the site is `data/pages/ccag/1/0112.json`.

```json
{
  "sheet": "0112",
  "page": 98,
  "lines": [
    {
      "n": 1,
      "id": "L-sdhgh2yc",
      "text": "ἱκανῶς ἐμπεφορτισμένος ἀπελεύσεται καὶ συντόμως συλληφθήσε-",
      "en": "sufficiently loaded he will depart and quickly he will be caught;"
    }
  ],
  "sections": [
    { "role": "pagenum", "text": "98", "en": "98" },
    { "role": "header", "text": "CODICES FLORENTINI", "en": "FLORENTINE CODICES" }
  ],
  "summary": { "headline": "…", "text": "…" }
}
```

| Field | What it holds |
|---|---|
| `sheet` | The sheet id, the same as the file name. |
| `page` | The number printed on the page, or `null` where the page prints none. The Columbia manuscript and the Māshāʾallāh number their leaves rather than their pages, so every page of theirs is `null`. |
| `numbering` | `"roman"` on a page of roman-numbered front matter; absent otherwise. |
| `lines` | The lines of the main text, top to bottom. |
| `lines[].n` | The line's position on the page, counting main-text lines only: line 1 is the first line of the body, below any running head. |
| `lines[].id` | The line's permanent id. |
| `lines[].text` | The transcription, as the page prints it, line-end hyphens included. |
| `lines[].en` | The English translation, or `null` where there is none — mostly a line that is a name, a number, or already English. |
| `sections` | Everything on the page outside the main text, in reading order: running heads, page numbers, footnotes, marginal notes, catchwords, captions. |
| `sections[].role` | What the section is: `header`, `pagenum`, `footnote`, `margin` and so on. |
| `sections[].text`, `sections[].en` | Its transcription and English, as for a line. Sections have no ids. |
| `summary` | A short description of the page, `{headline, text}`, where the page has one. |

### Ledgers and the line-id index

A ledger holds one JSON object per line of the file: the record written when an id was minted, and one more each time the id's line moved or was retired.

```json
{"id":"L-g3tt3hx5","minted":"2026-08-06","sheet":"0007","page":1,"ordinal":1,"norm":"catalogus","state":"live","joined":"2026-08-06"}
```

`norm` is the line's text with accents, case and punctuation removed; a later reading of the page is compared against it to decide which printed line is which. A retired id carries `"state": "retired"` and `successors`, the ids that replaced it when two lines were merged into one or one was split in two. To read a ledger, take each id's records in order, later fields overriding earlier ones.

The index says where each id stands now, one small file per first character of the id. `data/index/lines/ccag-1/s.json` holds every CCAG I id beginning `L-s`:

```json
{ "L-sdhgh2yc": { "p": 98, "n": 1 } }
```

`p` is the printed page and `n` the line. An entry carries `"numbering": "roman"` on a roman-numbered page and `"sh": "<sheet>"` where the page is found by its sheet; a retired id's entry is `{"s": [...]}`, its successors.

## Citing a line

A page's address is the book's address and the printed page number; a line adds its position or its id.

```
ccag/1/98                          CCAG I, printed page 98
ccag/1/vi                          roman-numbered front matter, page vi
ccag/1/98#L7                       line 7 of that page
ccag/1/98#L-k2f7q8mv               the line with that id
mashaallah-libri-tres/sheet/0012   a page with no printed number, by its sheet
```

The position (`#L7`) is what a person reads off the page. The id (`#L-k2f7q8mv`) stays with its printed line when a correction splits, merges or renumbers the lines around it, so it is the form to store. To look an id up, open the index file named for its first character after `L-`.

Where a sheet was scanned twice, two sheets bear the same printed number (CCAG IV, pages 12–13 and 146–147). The printed address names the first; the second copy is found by its sheet.

## Suggesting a correction

Open an issue with the **Suggest a correction** form. It asks for the book, page and line as the site shows them, what the page reads now, and what the scan shows. A report that a line looks wrong, without a proposed reading, is useful too.

## Licence

The data is dedicated to the public domain under [CC0 1.0 Universal](LICENSE). The books themselves are in the public domain; `catalogue.json` gives the basis for each and quotes the terms, if any, that the scan it was read from states.
