# Six KhianKrub changes (three as patch files) / การแก้ 6 เรื่องของเขียนครับ (แพตช์พร้อมใช้ 3 เรื่อง)

Patches against `kruboffice-desktop` `main` (KrubOffice 0.5.0, KhianKrub 0.17.0, commit `357885b`).
Each patch is one commit and applies on its own, except `0006`, which needs `0002` first. This repo holds `0001`–`0003`; `0004`–`0006` are described below and exist as patch files with the author. They touch
`sources/khiankrub` only (mostly `crates/ui-egui`). Code is under the project's own licence
(MIT OR Apache-2.0). No new image or font files: the new icons are drawn in code.

แพตช์ทำบน `main` ของ `kruboffice-desktop` (KrubOffice 0.5.0 / KhianKrub 0.17.0, commit `357885b`)
แต่ละไฟล์เป็น 1 commit ใส่แยกกันได้ ยกเว้น `0006` ที่ต้องใส่ `0002` ก่อน แตะเฉพาะ `sources/khiankrub`
ไม่มีไฟล์ภาพหรือฟอนต์ใหม่ (ไอคอนใหม่วาดด้วยโค้ด)

## How to apply / วิธีใส่

```sh
git checkout -b try-features 357885b                # or your current main
git am 0001-*.patch                                 # one at a time, any order (0006 after 0002)
git am -3 0001-*.patch 0002-*.patch 0003-*.patch 0004-*.patch 0005-*.patch 0006-*.patch   # all six
cargo test -p wordcraft-ui-egui --lib               # in sources/khiankrub
```

## 1. `0001` Underline menu with sample lines / เมนู Underline มีเส้นตัวอย่าง

The Underline drop-down lists each style as a drawn sample line (single, double, thick, dotted,
dashed, dot-dash, dot-dot-dash, wave) instead of only a name. Adds "Dot-dot-dash" and
"More Underlines…" (opens the Font dialog). Names stay as tooltips.
Files: `ribbon.rs` (new `underline_sample`), `i18n/th.tsv`.

## 2. `0002` Character Spacing button / ปุ่มระยะห่างตัวอักษร

A "Character Spacing" menu button in the Home › Font group: Very Tight, Tight, Normal, Loose,
Very Loose (`format.spacing` with points) and "More Spacing…" (Font dialog).
Files: `ribbon.rs`, new `custom_icons.rs` (the icon, drawn in code), `icons.rs` (2-line hook),
`lib.rs` (module line), `i18n/th.tsv`.

## 3. `0003` An object's distances from the paper edges / ระยะของวัตถุจากขอบกระดาษ

For a picture, shape or text box, so it can be placed exactly (e.g. an emblem 1.5 cm from the edges):
- while dragging: a label "Left: … Top: …" (or "W: … H: …" while resizing) in the options' unit;
- after an arrow-key nudge: the same label and the alignment guides, for 1.5 s;
- in the right-click menu: two lines, Left/Top and Right/Bottom, without moving the object.
Files: new `drag_readout.rs` (with tests), `objects.rs` (guide drawing split into `paint_guide_lines`;
the nudge hook), `canvas.rs` (menu lines), `lib.rs` (module line).

## 4. `0004` Clicking no longer scrolls the view / คลิกแล้วหน้าจอไม่เลื่อนเอง

> **Patch file not published in this repo yet** (only `0001`–`0003` are here). Ask the author for it. / ยังไม่มีไฟล์แพตช์ของข้อนี้ใน repo นี้ (มีแค่ 0001–0003) ขอได้จากผู้เขียน

Bug: clicking text (or selecting an object) near the top or bottom edge of the window scrolled the
view without the pointer moving. A click runs `caret.set`, which asked the canvas to follow the caret
with a 60 px margin. Now, after a click, the view only scrolls if the caret is really off screen, and
selecting an object by click no longer follows the object's anchor in the text.
บั๊ก: คลิกข้อความหรือเลือกวัตถุใกล้ขอบบน/ล่างของหน้าต่าง แล้วหน้าจอเลื่อนเอง ทั้งที่เมาส์ไม่ขยับ
Files: `canvas.rs` (a flag set by the click, read where the view follows the caret), `objects.rs`.
No regression test: the headless harness did not reproduce the edge case, so this one was
checked only by the author in the running window.

## 5. `0005` Click selects a header picture / คลิกเลือกรูปในหัวกระดาษ (เช่น ตราครุฑ)

> **Patch file not published in this repo yet** (only `0001`–`0003` are here). Ask the author for it. / ยังไม่มีไฟล์แพตช์ของข้อนี้ใน repo นี้ (มีแค่ 0001–0003) ขอได้จากผู้เขียน

Bug: while editing the header, a Behind Text picture (an emblem) could not be selected by clicking
where a header line lay over it, and a header picture repeated on page 2 could not be right-clicked.
Now a click on the picture where no header text covers it selects it, also over a blank header line
(which holds only the pictures' anchors), the text band is checked line by line, a right-click picks
it for the menu (Reset Picture, Wrap Text), and a repeated picture is selected on the page clicked.
Part of this comes from WordCraft #5601; the blank-line case is ours.
Files: `layout/hit.rs`, `ui-egui/objects.rs`, tests in `layout/tests.rs` and `canvas.rs`. Two canvas
tests that click and expect the picture are marked `#[ignore]`: KhianKrub's object pane opens when a
picture is selected and moves the page, so the harness's next frame handles the same press on the
text; the behaviour was confirmed by hand in the running window.

## 6. `0006` File › Print as a Word-style page / หน้า File › Print แบบ Word

> **Patch file not published in this repo yet** (only `0001`–`0003` are here). Ask the author for it. / ยังไม่มีไฟล์แพตช์ของข้อนี้ใน repo นี้ (มีแค่ 0001–0003) ขอได้จากผู้เขียน

The Print page is laid out like Word's: the Print button with Copies, the Printer, then the Settings,
each one a row with a picture (icons drawn in code): which pages, sides, collation, orientation,
paper size, margins and pages per sheet (1, 2, 4, 6, 9 or 16). Export to PDF with several pages per
sheet (N-up). Needs `0002` (it extends `custom_icons.rs`).
Files: `print_page.rs` (rewritten), `custom_icons.rs`, `engine/print.rs` (`pagesPerSheet`), `pdf/lib.rs`
(+tests), `tests/native_print.rs`, `examples/print_shot.rs` (a headless screenshot of the page),
`i18n/th.tsv` (about 40 strings), one test in `lib.rs` for the Print button.
Not checked: the page was looked at in headless screenshots on Windows only; printing through the
native path (`print_native`) was not run on a real printer, and macOS and Linux were not built.

## Checked / ผลตรวจ

- All six applied to `main`, built, in a separate target folder. `cargo test` for `ui-egui`, `engine`,
  `layout`, `pdf`: all pass, except 4 `ui-egui` tests that fail the same way on plain `main` on our
  Windows machine (`thai_heading_text_has_glyphs`, `the_most_seen_parts_are_in_thai`,
  `interface_symbols_have_glyphs`, `big_button_label_lines_fit_their_slot`), so the patches add no failure.
- `cargo clippy --all-targets` on those crates: no new warning (one old one at `print_page.rs`, which
  `0006` keeps from `main`).
- Each patch applies alone on `main` (`0006` after `0002`), and all six applied in order with `git am -3`
  give the tree that was tested.
- **Not checked / ยังไม่ได้ตรวจ:** the underline samples and character spacing icon were not compared side
  by side on a real display; macOS and Linux were not built.
