# polyglot

`crack-intros-amiga.pdf` is a research paper on crack intros. It is also a ZIP
archive, a bash script, and an MD5 hashquine. Run it, and it boots the Amiga
demo *My Kingdom* by Haujobb & Scoopex (1st place, The Party 1997) in your
browser, from a hard-disk image stored inside the file.

## Try it

- **As a PDF:** open it in any viewer. The 20 pages are the original paper, unchanged.
- **As a ZIP:** `unzip -l crack-intros-amiga.pdf` lists the emulator, the ROMs and the demo.
- **As a script:** `bash crack-intros-amiga.pdf [port] [bind-address]` starts a web
  server from inside the file (default `127.0.0.1:8000`). Open `/amiga/` and press
  **Play**, or **Cold boot** to start the machine from power-on.

You need bash, Python 3.11 or newer, and a current browser. It has been tested in
Chromium. Open it as `127.0.0.1:8000` the emulator only allows plain-http downloads on 
those addresses, so serving it to other machines on your network won't work.

## How it works

| Layer | Where it lives |
|---|---|
| bash | Line 1, `: <<"PDFSTART"`, swallows the PDF header as a heredoc. The launcher sits in an unreferenced PDF object straight after it and `exec`s Python. |
| PDF | The original paper, rewritten by qpdf into a single cross-reference table. Its offsets are shifted by the inserted objects and count from the `%PDF` line, not byte 0. |
| ZIP | An unreferenced object after the trailer. The ZIP's end-of-archive comment is the PDF's own `startxref … %%EOF` tail. |
| Hashquine | 128 MD5 collision blocks right after the header, made with fastcoll. Either block of each pair gives the same MD5, so one block per pair spells out one bit of the file's own hash. The launcher refuses to run if the hash doesn't match, like PoC‖GTFO 0x16 did. |
| Emulator | The scene.graphics Amiga player, byte-identical to the site. Your browser checks it, the ROMs and the demo against Niklas Femerstrand's Ed25519 signatures before the machine starts. |

## Credits and licences

- **Paper:** Markku Reunanen, Patryk Wasiak & Daniel Botz, "Crack Intros: Piracy,
  Creativity, and Communication", *International Journal of Communication* 9 (2015),
  798–817, <https://ijoc.org/index.php/ijoc/article/view/3731>. © 2015 the authors,
  licensed under CC BY-NC-ND. Included unmodified.
- **Demo:** *My Kingdom* by Haujobb & Scoopex, The Party 1997,
  <https://scene.graphics/amiga/my-kingdom-haujobb-scoopex>.
- **Emulator and ROMs:** Niklas Femerstrand, <https://scene.graphics>. The `hm-roms`
  Kickstart replacement is his own work, written from scratch, © 2026 Niklas Femerstrand,
  all rights reserved. It is included here with his permission.
- **Inspiration:** PoC‖GTFO 0x16 by Travis Goodspeed and friends, whose polyglot,
  launcher and MD5 self-check this follows.
- **fastcoll:** Marc Stevens, <https://github.com/cr-marcstevens/hashclash>.
