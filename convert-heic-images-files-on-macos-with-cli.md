---
description: converting HEIC images to JPEG, JPG format etc.
---

# Convert HEIC images/files on macOS with CLI

This is a note to self.

**A historical context:** I juggle between my roles as a developer and an amateur iPhone photographer, and I faced a recurring challenge. iOS, in its wisdom, captured my memories in HEIC format. Perfect for storage, but not so friendly when I needed to share or upload those snaps across various platforms. Many apps just raised an eyebrow at HEIC, leaving me in a bind.

The web was filled with conversion tools, but as a developer, opening a browser, uploading, and then downloading converted files felt...inefficient. My developer instincts craved a streamlined option to do this, something right from the terminal.

Enter `sips` - macOS's unsung command-line hero, present since Mac OS X 10.3 (Panther). A tool that could convert and edit images without breaking a sweat.\
\
Unofficial `sips` CLI reference: [https://ss64.com/osx/sips.html](https://ss64.com/osx/sips.html)

\
**Straightforward basic image conversions : `sips`** :fire:

```bash
sips -s format jpeg file.HEIC --out file.jpg
```

**Batch conversion:** :school\_satchel:

```bash
for i in *.heic(:r); do sips -s format jpeg "$i.heic" --out "$i.jpg"; done;
```

_Note: `(:r)` is a specific feature of the zsh shell's filename generation, where it strips away the file extension. So, when you use \*.heic(:r), it would transform filenames like a.heic and b.heic to just a and b._

**Feeling brave? Convert and replace:** :superhero:

If you want to also delete the originals while converting the files, follow up with a `rm *.heic` – or use this variant instead:

```
for i in *.heic(:r); do sips -s format jpeg "$i.heic" --out "$i.jpg" && rm "$1.heic"; done;
```
