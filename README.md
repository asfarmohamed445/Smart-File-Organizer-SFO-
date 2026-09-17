# Smart File Organizer (SFO)

SFO is a highly reliable, safety-focused terminal utility for organizing your files automatically. 
It analyzes, categorizes, and organizes your files into neat subdirectories based on built-in rules or your custom configurations—**built with absolutely zero third-party dependencies**.

## 🚀 Features

- **Zero Dependencies**: SFO uses only the Python standard library. No `rich`, `tqdm`, `click`, or `pytest`. It's lightning-fast and installs instantly on any system running Python 3.11+.
- **Dry-Run by Default**: Safety first. Every move is calculated and presented in a beautiful CLI table. SFO will not move a single file until you append `--apply`.
- **Instant Undo Engine**: Made a mistake? Run `sfo undo .` to instantly reverse your last organization batch. All history is tracked locally in a safe, versioned transaction log.
- **Duplicate Detection**: SFO ships with a robust `hashlib.sha256` hashing engine supporting 64KB chunking to safely process multi-gigabyte files without memory exhaustion.
- **Production Hardened**: Built-in concurrent execution locking (`.sfo_lock`), circular symlink protection, path traversal protection, and cross-device partition support via `shutil` fallback.
  - *Note on Symlinks*: Symlinks are **not followed by default** (`follow_symlinks = false`). On Windows, symlink resolution may behave differently or require Developer Mode / Administrative privileges compared to macOS and Linux environments. SFO safely ignores them if unresolvable.

## 📦 Installation

SFO can be installed globally via pip directly from the source code:

```bash
pip install -e .
```

This makes the `sfo` command available everywhere on your system!

## 💻 Usage

### 1. Scan and Analyze
Discover what's in a directory without touching anything:
```bash
sfo scan ./Downloads
sfo analyze ./Downloads
```

### 2. Classify and Organize
Preview how files will be categorized. By default, SFO creates folders like `Images/`, `Documents/`, `Source Code/`, etc.
```bash
sfo classify ./Downloads
sfo organize ./Downloads
```

Once you're happy with the "Move Plan" table, execute it safely:
```bash
sfo organize ./Downloads --apply
```

### 3. Revert Mistakes
If you accidentally organized the wrong folder, simply revert it:
```bash
sfo undo ./Downloads
```

### 4. Find Duplicates
Detect duplicate files taking up storage:
```bash
sfo duplicates ./Downloads
```

## 🛠️ Custom Configuration (`SFOfile`)

You can create an `SFOfile` in any directory to define your own categories! SFO will parse it natively without any third-party YAML/JSON parsers.

Example `SFOfile`:
```ini
[settings]
skip_hidden = true
follow_symlinks = false

[rules]
.mp3 = Music
.wav = Music
.pdf = Important Docs
.py = Python Scripts
```

## 🚪 Exit Codes

SFO uses consistent exit codes for robust scripting:
- `0` - Success
- `1` - General Error (e.g., partial move failures)
- `2` - Argument/Command Error
- `3` - Configuration Error
- `4` - Filesystem Error (e.g., directory not found)
- `5` - Lock Error (another instance is running)
- `6` - Interrupted (e.g., Ctrl+C)


## 📄 License
MIT License.
