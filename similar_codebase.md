# Similar Markdown Editors — Analysis

MarkText features used as baseline: WYSIWYG inline editing, source mode, focus/typewriter mode, themes, math (KaTeX), diagrams (Mermaid), multiple export formats.

## Comparison Table

| Editor | Stack | Open Source | Node.js to build? | Notes |
|---|---|---|---|---|
| **Typora** | C++/WebKit | No (closed source) | No | Most similar UX; WYSIWYG inline editing |
| **Zettlr** | Electron | Yes | Yes | Very similar feature set; same toolchain pain |
| **Ghostwriter** | C++ / Qt | Yes | No | Focus mode, themes, math via KaTeX, distraction-free |
| **Apostrophe** | Python / GTK | Yes | No | GNOME-native, focus mode, live preview |
| **ReText** | Python / Qt | Yes | No | Simple split-pane preview, lightweight |
| **Glow** | Go | Yes | No | Terminal-only renderer, no GUI |
| **Obsidian** | Electron | No (closed source) | No (prebuilt) | Notes/PKM focus, not a pure markdown editor |
| **Joplin** | Electron | Yes | Yes | Notes app, same Node.js build requirement |

## Math Rendering Support (`$...$` inline and `$$...$$` block)

| Editor | Math support | Engine | Notes |
|---|---|---|---|
| **Ghostwriter** | Yes, both | KaTeX (via Pandoc) | Requires Pandoc installed; see setup below |
| **Typora** | Yes, both | MathJax / KaTeX | Built-in, no extra setup |
| **Zettlr** | Yes, both | KaTeX | Built-in |
| **Obsidian** | Yes, both | MathJax | Built-in |
| **Joplin** | Yes, both | KaTeX | Built-in |
| **Apostrophe** | Partial — `$$` block only | MathJax via Pandoc | Inline `$...$` unreliable |
| **ReText** | No native support | Python-Markdown | Needs manual extension setup (`pymdownx.arithmatex`); no built-in math |
| **Glow** | No | Terminal only | — |

## Best Node.js-free alternatives on Ubuntu

### Ghostwriter (recommended)

Closest feature parity to MarkText. Supports focus mode, typewriter mode, themes, math, and Mermaid diagrams. Written in C++/Qt.

**Install:**

```bash
sudo apt install ghostwriter
```

**Important:** Ghostwriter 2.x requires an external markdown processor — without one, the preview does not render at all (no output, no math, no formatting). Install Pandoc:

```bash
sudo apt install pandoc
```

Verify both are available:

```bash
ghostwriter --version   # should report 2.x and confirm Pandoc is found
pandoc --version
```

**Enable math rendering in Ghostwriter:**

1. Open Ghostwriter → **Preferences → Preview**
2. Set processor to **Pandoc**
3. Enable **MathJax** or **KaTeX** under math options
4. Relaunch Ghostwriter

After this, both `$...$` inline and `$$...$$` block math render correctly.

### Apostrophe

Clean GNOME-native option. Minimalist design, focus mode, live preview. Written in Python/GTK.

```bash
flatpak install flathub org.gnome.gitlab.somas.Apostrophe
```

Note: math support is partial — block `$$...$$` works but inline `$...$` is unreliable.

## Ghostwriter — Known Limitations

### No synchronized scrolling (confirmed missing as of 2026)

**Symptom:** When editing near the bottom of a document, the HTML preview pane scrolls back to the top. Manually scrolling one pane does not scroll the other.

**Status:** Never implemented in any released version, including the latest KDE-versioned releases (`25.12.3`, released 2026-03-05). Two open GitHub discussions confirm the feature is still requested and unresolved:
- [Synchronized scrolling · Discussion #662](https://github.com/KDE/ghostwriter/discussions/662)
- [Synchronize the HTML-Preview · Discussion #858](https://github.com/KDE/ghostwriter/discussions/858)

**Why it's hard:** The markdown source and the rendered HTML occupy different amounts of vertical space in a way that is not consistent — a single heading in markdown may expand to a large block in the preview, making a simple line-ratio scroll mapping inaccurate. The discussions acknowledge this as a non-trivial design problem.

**Contrast with MarkText:** MarkText uses a WYSIWYG inline model — there is no separate preview pane, so the sync scrolling problem does not exist by design.

**Potential contribution:** This is an open and explicitly requested feature in Ghostwriter. A viable approach used by other editors (e.g. Typora, VS Code Markdown preview) is to inject anchor IDs into rendered HTML elements and match them to source line numbers. Worth investigating Ghostwriter's C++/Qt preview rendering pipeline if contributing there.

---

## Known MarkText Bugs Worth Investigating

### `$$...$$` display math not rendering correctly (v0.17.1)

**Symptom:** Block math delimited by `$$...$$` does not render as a proper display (centred, full-width) equation — it either renders inline or the KaTeX `displayMode` flag is not applied correctly.

**Evidence from source code:**

- [src/muya/lib/assets/styles/index.css:319](src/muya/lib/assets/styles/index.css#L319) contains an explicit TODO comment:
  > `/* TODO: DOM path should be wrong. Correct path should be: 'ag-container-preview > .katex-display'. */`
  The CSS rule targeting `.ag-math > .ag-math-render .katex-display` is applied to the wrong DOM node, meaning display math styles (centring, block layout) are never triggered.

- [src/muya/lib/utils/exportHtml.js:122](src/muya/lib/utils/exportHtml.js#L122) — KaTeX is called with a `displayMode` boolean, which is correct in the export path. The bug appears to be isolated to the live preview rendering path.

- [src/muya/lib/utils/importMarkdown.js:164](src/muya/lib/utils/importMarkdown.js#L164) — `$$...$$` blocks are parsed as `multiplemath` type, which is the correct internal representation.

**Workaround:** Insert math blocks via the menu (**Paragraph → Math Block**, shortcut `Ctrl+Alt+M`) rather than typing `$$` manually. This may trigger the correct block type and bypass the parser issue.

**Potential fix:** Correct the CSS selector in [src/muya/lib/assets/styles/index.css:320](src/muya/lib/assets/styles/index.css#L320) from `.ag-math > .ag-math-render .katex-display` to `.ag-container-preview > .katex-display` as suggested by the TODO, and verify the live preview rendering path passes `displayMode: true` to KaTeX consistently with the export path.

**Good first issue candidate:** The TODO comment confirms the core team was already aware of this. A fix would be a small, well-scoped CSS + rendering pipeline change.

---

## Notes on contributing to Node.js-free alternatives

If the goal is to **contribute to a markdown editor without the Electron/Node.js toolchain**, both Ghostwriter (C++/Qt) and Apostrophe (Python/GTK) are significantly easier to build on Ubuntu than Electron-based editors like MarkText or Zettlr.
