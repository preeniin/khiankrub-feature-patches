# Three small KhianKrub features / ฟีเจอร์เล็ก 3 อย่างของเขียนครับ

Patches against `kruboffice-desktop` `main` (KrubOffice 0.5.0, KhianKrub 0.17.0, commit `357885b`).
Each patch is one commit and applies on its own. They touch only `sources/khiankrub/crates/ui-egui`.
Code is under the project's own licence (MIT OR Apache-2.0). No new image or font files: the one new
icon is drawn in code.

แพตช์ทำบน `main` ของ `kruboffice-desktop` (KrubOffice 0.5.0 / KhianKrub 0.17.0, commit `357885b`)
แต่ละไฟล์เป็น 1 commit ใส่แยกกันได้ แตะเฉพาะ `sources/khiankrub/crates/ui-egui` ไม่มีไฟล์ภาพหรือฟอนต์ใหม่
(ไอคอนใหม่ 1 อัน วาดด้วยโค้ด)

## How to apply / วิธีใส่

```sh
git checkout -b try-features 357885b          # or your current main
git am 0001-*.patch                           # one at a time, any order
git am -3 0001-*.patch 0002-*.patch 0003-*.patch   # all three together
cargo test -p wordcraft-ui-egui --lib         # in sources/khiankrub
```

## 1. `0001` Underline menu with sample lines / เมนู Underline มีเส้นตัวอย่าง

The Underline drop-down lists each style as a drawn sample line (single, double, thick, dotted,
dashed, dot-dash, dot-dot-dash, wave) instead of only a name. Adds "Dot-dot-dash" and
"More Underlines…" (opens the Font dialog). Names stay as tooltips.
เมนูลูกศรของปุ่ม Underline แสดงเส้นแต่ละแบบเป็นภาพตัวอย่าง เพิ่ม "เส้นจุดจุดประ" และ "เส้นใต้เพิ่มเติม…"
Files: `ribbon.rs` (new `underline_sample`), `i18n/th.tsv`.

## 2. `0002` Character Spacing button / ปุ่มระยะห่างตัวอักษร

A "Character Spacing" menu button in the Home › Font group: Very Tight, Tight, Normal, Loose,
Very Loose (`format.spacing` with points) and "More Spacing…" (Font dialog). Same choices as Word.
ปุ่มระยะห่างระหว่างอักขระในกลุ่มฟอนต์ของแท็บ Home: แคบมาก/แคบ/ปกติ/กว้าง/กว้างมาก และ "ระยะห่างเพิ่มเติม…"
Files: `ribbon.rs`, new `custom_icons.rs` (icon drawn in code), `icons.rs` (2-line hook), `lib.rs`
(module line), `i18n/th.tsv`.

## 3. `0003` An object's distances from the paper edges / ระยะของวัตถุจากขอบกระดาษ

For a picture, shape or text box, so it can be placed exactly (e.g. an emblem 1.5 cm from the edges):
- while dragging: a label "Left: … Top: …" (or "W: … H: …" while resizing) in the options' unit;
- after an arrow-key nudge: the same label and the alignment guides, for 1.5 s;
- in the right-click menu: two lines, Left/Top and Right/Bottom, without moving the object.
ป้ายระยะ ซ้าย/บน (หรือ กว้าง/สูงตอนปรับขนาด) ตอนลาก, หลังกดปุ่มลูกศร (พร้อมเส้นไกด์ 1.5 วินาที)
และสองบรรทัดระยะ ซ้าย/บน/ขวา/ล่าง ในเมนูคลิกขวา โดยไม่ต้องขยับวัตถุ
Files: new `drag_readout.rs` (with tests), `objects.rs` (the guide drawing is split into
`paint_guide_lines`; the nudge hook), `canvas.rs` (menu lines), `lib.rs` (module line).

## Checked / ผลตรวจ

- `cargo test -p wordcraft-ui-egui --lib` on `main` + all three patches: 611 passed, 4 failed. The same 4
  fail on plain `main` on our Windows machine (`thai_heading_text_has_glyphs`,
  `the_most_seen_parts_are_in_thai`, `interface_symbols_have_glyphs`, `big_button_label_lines_fit_their_slot`),
  so the patches add no failure. 3 new tests (distance label text, the 1.5 s nudge window, the menu lines).
- `cargo clippy -p wordcraft-ui-egui --all-targets`: no new warning (one old one in `print_page.rs`).
- **Not checked / ยังไม่ได้ตรวจ:** the looks on screen of the underline samples and the character spacing
  icon, and the label while dragging, were not compared side by side on a real display; the nudge
  guides and the menu lines were not seen in the running window by anyone but the author's tests.
  macOS and Linux were not built.
