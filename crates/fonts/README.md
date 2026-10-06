# printcraft-fonts

Layer L2. Font helpers for the appearance streams PrintCraft generates (comments, form fields).

Today it holds what generated appearances need without a font program:

- `helvetica_width`: an approximation of Helvetica's proportions by character class. No metrics
  file or font program from any vendor is bundled; widths are PrintCraft's own estimates, good
  enough for line breaking and alignment, not for typesetting.
- `wrap`: greedy line breaking with that measure (paragraphs on newlines, long words split).
- `win_ansi`: Unicode → WinAnsiEncoding bytes (`?` for characters it can't represent), and
  `literal` to write bytes as a PDF literal string.

It also holds the fonts of the optional [craft-fonts](https://github.com/storytold/craft-fonts)
build input. `build.rs` embeds them as `CRAFT_FONTS` when the build sets
`CRAFT_FONTS_DIR=<checkout>` (and fails if it can't while `CRAFT_FONTS_REQUIRED=1`, as releases
set). Without that variable `CRAFT_FONTS` is empty and everything below copes:

- `ui_japanese_fonts`: the `Jpan` faces for the interface, BIZ UDPGothic first.
- `ui_chinese_fonts`: the `Hans` faces for the interface, in manifest order (the allowed
  `Hans` face comes from craft-fonts; without that build input Chinese text shows
  the font system's replacement glyph).
- `ui_cjk_fonts(prefer_hans)`: both in fallback order for the UI language (Chinese group first
  in Chinese mode, so one line never mixes faces with different vertical metrics).
- `document_japanese_font` / `document_chinese_font` / `japanese_glyph`: the faces (Shippori
  Mincho, then BIZ UDMincho; any regular `Hans` face for Chinese) whose outlines become the
  Type 3 fallback font for CJK text written into PDFs. Without it,
  `japanese_glyph` returns `GlyphError::NoFont` and the editor reports a clear error.
- `SHIPPORI_MINCHO`: Shippori Mincho's bytes, or `None`.

wasm32 builds embed only BIZ UDPGothic Regular, to keep the web build small: the web build
currently has no Chinese face, so Chinese there still shows the replacement glyph.
Noto CJK is never embedded (AGENTS.md §1.1), even if a craft-fonts checkout still lists it.
Font files are never
committed here (`AGENTS.md` §1.4; team members: [craftrules `standards/fonts.md`](https://github.com/storytold/craftrules/blob/main/standards/fonts.md), internal).

The full font subsystem (parsing, shaping, subsetting and embedding) arrives with M2.2/M7.
