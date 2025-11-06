# richmark: Markdown ⇄ Rich Text

A standalone, static-file bidirectional Markdown and Rich Text editor that works entirely in your browser.

Some apps, like Google Docs, have good Copy as Markdown/Paste as Markdown support.

Others don't, so `richmark` is a simple tool to make it easier to move between Rich Text and Markdown, and into and out of the clipboard.

Some specialized support for Obsidian markdown is also provided.


## Overview

This is a static HTML file that provides real-time, bidirectional conversion between Markdown and Rich Text (WYSIWYG) editing. Simply open `index.html` in your browser and start editing - no server, no installation, no dependencies required.

## Features

### Core Functionality

- **Live Bidirectional Sync**: Edit in either pane and see changes reflected instantly in the other
- **Markdown Support**: Full GitHub Flavored Markdown (GFM) including:
  - Headings (`#` through `######`)
  - Bold, italic, strikethrough
  - Inline code and fenced code blocks
  - Blockquotes
  - Ordered and unordered lists (including nested lists)
  - Task lists (`- [ ]` / `- [x]`)
  - Links (with auto-linking)
  - Images
  - Tables with alignment
  - Horizontal rules
- **Rich Text Editing**: Full WYSIWYG editing with `contenteditable`
- **Smart Copy**:
  - Copy Markdown as plain text
  - Copy Rich Text as HTML (with plain text fallback)
- **Smart Paste**:
  - Automatically detects and converts Markdown when pasting
  - Sanitizes HTML for security
  - Preserves formatting where possible
  - Handles complex tables from Google Docs with colspan support
- **Markdown Transforms**:
  - Strip Wiki Links: Converts Obsidian-style `[[links]]` to plain text
  - Wiki Links → Markdown Links: Converts `[[target|display]]` to `[display](target)`
  - Strip Comments: Removes Obsidian-style `%% comment %%` blocks
- **Obsidian Compatibility**:
  - Automatically hides `%% comment %%` blocks in rich text view
  - Supports wiki-style links `[[page]]` and `[[page|display]]`

### User Experience

- **Resizable Panes**: Drag the divider to adjust the split (defaults to 50-50)
- **Responsive Design**: Works on desktop and mobile
  - Two-column layout on screens ≥ 900px
  - Stacked layout on smaller screens
- **Dark Mode**: Automatically adapts to your system preferences
- **Keyboard Shortcuts**:
  - `Cmd/Ctrl+B`: Bold (in Rich Text)
  - `Cmd/Ctrl+I`: Italic (in Rich Text)
  - `Cmd/Ctrl+K`: Insert link (in Rich Text)
  - `Cmd/Ctrl+Enter`: Switch between panes
  - `Shift+Cmd/Ctrl+V`: Paste as plain text (in Rich Text)
- **Persistence**: Automatically saves your work to browser storage
- **Accessibility**: Full keyboard navigation and ARIA labels

### Security

- **XSS Protection**: All HTML is sanitized with DOMPurify
- **Safe Links**: External links automatically open with `rel="noopener noreferrer"`
- **No Telemetry**: Your data stays in your browser

## Usage

### Getting Started

1. Open `index.html` in a modern web browser
2. Start typing Markdown in the left pane, or
3. Start editing Rich Text in the right pane

That's it! The conversion happens automatically.

### Copying Content

- Click **Copy Markdown** to copy the plaintext Markdown to your clipboard
- Click **Copy Rich Text** to copy the formatted HTML (works in email clients, document editors, etc.)

### Pasting Content

- Paste Markdown or plain text into either pane
- Paste formatted content (from Word, Google Docs, etc.) into the Rich Text pane
- Use `Shift+Cmd/Ctrl+V` in Rich Text to paste without formatting

### Using Transforms

The Transforms dropdown in the Markdown toolbar provides quick text transformations:

1. **Strip Wiki Links**: Removes Obsidian-style wiki link syntax
   - `[[target]]` becomes `target`
   - `[[target|display text]]` becomes `display text`

2. **Wiki Links → Markdown Links**: Converts wiki links to standard Markdown
   - `[[target]]` becomes `[target](target)`
   - `[[target|display text]]` becomes `[display text](target)`

3. **Strip %% %% Comments**: Permanently removes all comment blocks from Markdown
   - Useful for cleaning up before export
   - Note: Comments are already hidden in rich text view

### Clearing the Editor

Click the **Clear** button in the header to reset both panes and clear saved content.

## Technical Details

### Architecture

- **Pure Vanilla JavaScript**: No frameworks, no build step
- **Single HTML File**: Everything is self-contained
- **CDN Dependencies** (can be vendored for offline use):
  - `markdown-it`: Markdown to HTML conversion
  - `turndown`: HTML to Markdown conversion
  - `turndown-plugin-gfm`: GitHub Flavored Markdown support
  - `DOMPurify`: HTML sanitization

### Browser Compatibility

Works in the latest stable versions of:
- Chrome
- Edge
- Firefox
- Safari

Requires modern Clipboard API support for optimal copy/paste functionality (with fallbacks for older browsers).

### Performance

- Debounced conversions (150ms) for smooth typing
- Handles documents up to ~100kB efficiently
- No network required after initial load (if dependencies are vendored)
- Custom table conversion handles complex tables with merged cells (colspan)
- Automatic normalization of pasted content from Google Docs and other sources

### Data Persistence

- Content is saved to `localStorage` under the key `mdrt.content.v1`
- Saves both Markdown and HTML versions
- Preserves pane split ratio
- Survives browser restarts
- Use the Clear button to reset

## Round-Trip Support

Most Markdown features round-trip perfectly between formats:

| Feature | Round-trip | Notes |
|---------|------------|-------|
| Headings | ✅ | All levels (#-######) |
| Bold/Italic | ✅ | Preserved |
| Strikethrough | ✅ | GFM support |
| Code | ✅ | Inline and blocks |
| Lists | ✅ | Including nested |
| Task Lists | ✅ | GFM checkboxes |
| Links | ✅ | Auto-linking enabled |
| Images | ✅ | With sanitization |
| Tables | ✅ | GFM tables with alignment, colspan support |
| Blockquotes | ✅ | Preserved |
| Horizontal Rules | ✅ | Preserved |
| Underline | ⚠️ | Kept as inline HTML |
| Superscript/Subscript | ⚠️ | Kept as inline HTML |
| Wiki Links | ➖ | Not converted (use transforms) |
| Comments `%% %%` | ➖ | Hidden in rich text, use transform to strip |

**Notes:**
- Elements without Markdown equivalents (like `<u>`, `<sup>`, `<sub>`) are preserved as inline HTML
- Obsidian-style wiki links `[[page]]` and comments `%% text %%` are preserved in Markdown but can be transformed
- Complex tables with merged cells (colspan) are converted to standard Markdown tables with padding

## Customization

The application uses CSS variables for theming. Modify the `:root` variables in the `<style>` section to customize colors:

```css
:root {
  --bg: #0b0c0f;      /* Background */
  --fg: #e6e7ea;      /* Foreground text */
  --muted: #a9acb3;   /* Muted text */
  --panel: #14161a;   /* Panel background */
  --border: #2a2e35;  /* Borders */
  --accent: #4c8bf5;  /* Accent color */
}
```

## Offline Use

To use completely offline, vendor the CDN scripts inline:

1. Download the minified versions of the dependencies
2. Replace the `<script src="...">` tags with inline `<script>` blocks
3. Paste the minified code inside

## Privacy

- No data leaves your browser
- No analytics or tracking
- No server communication (after initial load)
- Content is only saved to your browser's localStorage

## License

This project is provided as-is for educational and personal use.

## Development

### State Management

The editor uses a simple state machine:
- `lastEdited`: Tracks which pane was last modified ('md' or 'rich')
- `syncing`: Prevents feedback loops during bidirectional updates
- Debounced conversions prevent performance issues

### Security Model

All rendered and pasted HTML is sanitized through DOMPurify with the HTML profile. Script tags, event handlers, and `javascript:` URLs are blocked.

### Adding Custom Transforms

You can easily add custom text transformations by editing the `transforms` object in the JavaScript:

```javascript
const transforms = {
  'my-transform': (text) => {
    // Your transformation logic here
    return text.replace(/pattern/g, 'replacement');
  }
};
```

Then add a corresponding option to the `<select>` menu:

```html
<option value="my-transform">My Transform</option>
```

## Recent Updates

**Transform Features:**
- Added Transforms dropdown menu for Markdown text processing
- Strip Wiki Links: Convert Obsidian `[[links]]` to plain text
- Wiki Links → Markdown: Convert to standard Markdown link syntax
- Strip Comments: Remove `%% comment %%` blocks

**Table Support:**
- Custom table conversion with colspan support
- Proper handling of complex Google Docs tables
- Automatic padding for merged cells
- Clean round-trip conversion

**Obsidian Compatibility:**
- Comments `%% text %%` automatically hidden in rich text view
- Wiki link syntax preserved in Markdown
- Transform tools for converting Obsidian syntax

## Roadmap / Future Enhancements

Potential additions (not currently implemented):
- Math/LaTeX support (KaTeX)
- Syntax highlighting for code blocks (highlight.js)
- Export to .md or .html files
- Import from files
- Multiple themes
- Additional Obsidian syntax support (callouts, embeds)
- Custom keyboard shortcuts
- Offline-first vendored version

## Acknowledgments

Built with:
- [markdown-it](https://github.com/markdown-it/markdown-it)
- [Turndown](https://github.com/mixmark-io/turndown)
- [DOMPurify](https://github.com/cure53/DOMPurify)
