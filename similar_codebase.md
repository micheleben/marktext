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

## Best Node.js-free alternatives on Ubuntu

### Ghostwriter (recommended)
Closest feature parity to MarkText. Supports focus mode, typewriter mode, themes, math via KaTeX, and Mermaid diagrams. Written in C++/Qt — easy to build on Ubuntu.

```bash
sudo apt install ghostwriter
```

### Apostrophe
Clean GNOME-native option. Minimalist design, focus mode, live preview. Written in Python/GTK.

```bash
flatpak install flathub org.gnome.gitlab.somas.Apostrophe
```

## Notes on contributing to Node.js-free alternatives

If the goal is to **contribute to a markdown editor without the Electron/Node.js toolchain**, both Ghostwriter (C++/Qt) and Apostrophe (Python/GTK) are significantly easier to build on Ubuntu than Electron-based editors like MarkText or Zettlr.
