# Smart File Organizer (SFO) - Detailed Project Documentation

This document serves as the **comprehensive, single-source-of-truth** detailing everything that has been implemented in the **Smart File Organizer (SFO)** project.

---

## 1. Project Overview & Philosophy

**Smart File Organizer (SFO)** is a robust, highly reliable, safety-focused terminal utility designed to analyze, categorize, and organize files automatically into directories based on built-in or custom rules. 

### The "Zero-Dependency" Mandate
The core philosophy of this project was to build a professional-grade CLI utility using **absolutely zero third-party libraries**. 
Instead of relying on common tools like `click`, `rich`, `tqdm`, `pytest`, or `pyyaml`, every system was built natively from the ground up using only the Python standard library. This ensures maximum compatibility and instant installation on any system running Python 3.10+.

---

## 2. Core Architecture & Modules

The source code is organized within the `sfo/` directory, divided into highly cohesive modules:

### Data Models & Discovery
- **`models.py`**: Contains the `FileItem` class. This is the foundational data structure that stores absolute paths, byte sizes, file extensions, and safety metadata (like whether a file is hidden or read-only).
- **`scanner.py`**: A robust directory discovery engine powered by `os.scandir`. It includes native OS calls (using `ctypes` on Windows) to accurately identify and safely skip hidden and system-protected files.

### Business Logic & Rules
- **`config.py`**: A custom INI-style parser designed to read local `.SFOfile` configuration files, bypassing the need for `yaml` or `json`.
- **`rules.py` & `classifier.py`**: The brain of the organizer. It maps files to target categories (e.g., `.mp3` -> `Music/`, `.py` -> `Source Code/`). It intelligently merges default system rules with user-provided overrides.

### The Execution Engine
- **`organizer.py`**: Implements a strict **Planner-Executor** pattern. When users run the organize command, a `MovePlan` is generated in memory. Nothing is written to disk unless explicitly commanded.
- **`filesystem.py`**: Provides safe and robust wrappers around `os.rename` and `shutil.move`. It handles cross-device partitions gracefully and prevents accidental data overwrites through name-collision detection.

### History & Safety
- **`history.py`**: Implements a transaction-logging engine. Every file move is serialized into a custom, pipe-delimited flat file (`.sfo_history`). This powers the `sfo undo` command, allowing users to instantly revert massive directory restructurings.
- **`duplicates.py`**: A hyper-fast, zero-dependency duplication finder. It first groups files by exact byte size (an $O(N)$ operation) and then uses a robust 64KB chunked `hashlib.sha256` checksum to safely find exact matches among large files.

### UI & Presentation
- **`terminal.py`**: A custom rendering engine built to replace `rich`. It manages ANSI escape codes for cross-platform color output, calculates dynamic column widths to render beautiful ASCII tables, and creates single-line animated progress bars.
- **`cli.py`**: A custom command router that parses `sys.argv` natively to dispatch commands.
- **`analyzer.py`**: Aggregates scanned files and computes total storage consumed by category, presenting the data in human-readable sizes (KB, MB, GB).

---

## 3. Project Structure

```text
SFO/
├── .github/workflows/ci.yml         # Continuous Integration 
├── pyproject.toml                   # Standard Python packaging metadata
├── README.md                        # Quick-start guide
├── PROJECT_SUMMARY.md               # Original summary
├── SFOfile                          # Example local configuration file
│
├── sfo/                             # Main Package Directory
│   ├── __init__.py
│   ├── __main__.py                  # Global entrypoint for the CLI
│   ├── analyzer.py                  # Storage analysis logic
│   ├── classifier.py                # File categorization engine
│   ├── cli.py                       # Argument parsing and routing
│   ├── config.py                    # Custom SFOfile parser
│   ├── duplicates.py                # Fast duplicate detection
│   ├── errors.py                    # Custom exception definitions
│   ├── filesystem.py                # Safe file operations
│   ├── history.py                   # Transaction logging and Undo logic
│   ├── models.py                    # Data structures
│   ├── organizer.py                 # Planner-Executor architecture
│   ├── rules.py                     # Extension mapping rules
│   ├── scanner.py                   # Directory walker
│   ├── terminal.py                  # Custom ASCII tables & progress bars
│   └── test_runner.py               # Custom unit-testing framework
│
└── tests/                           # Comprehensive Test Suite
    ├── benchmark.py                 # Performance benchmarking scripts
    ├── run_all.py                   # Script to execute all tests
    ├── test_acceptance.py           # End-to-end integration tests
    ├── test_analyzer.py
    ├── test_classifier.py
    ├── ... (10+ test modules)
```

---

## 4. How to Use SFO

Because SFO is packaged with `pyproject.toml` and `hatchling`, installing it locally hooks the `sfo` command directly into your terminal environment.

```bash
# Install the project globally
pip install -e .
```

### Supported Commands

1. **Analysis & Discovery** (Read-only)
   - `sfo scan ./Downloads` — Scans the directory and prints statistics.
   - `sfo analyze ./Downloads` — Groups files by category and calculates total storage space used.

2. **Categorization & Moving**
   - `sfo classify ./Downloads` — Previews how files will be categorized.
   - `sfo organize ./Downloads` — Generates and prints a **Move Plan** (Dry-run mode by default).
   - `sfo organize ./Downloads --apply` — Executes the moves safely on the disk.

3. **Reverting Mistakes**
   - `sfo undo ./Downloads` — Reads the hidden transaction log and moves every file back to its exact original location prior to the last `organize` command.

4. **Duplicate File Detection**
   - `sfo duplicates ./Downloads` — Finds redundant files to help free up storage space.

---

## 5. Custom Configuration (`SFOfile`)

SFO supports per-directory configuration overrides. Users can drop an `SFOfile` in any directory to customize behavior. The custom parser reads standard `INI` syntax without relying on third-party parsers:

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

---

## 6. Testing Framework

Since third-party libraries like `pytest` were prohibited, SFO ships with its own robust testing engine (`sfo/test_runner.py`).
- It dynamically discovers `test_*.py` files using `importlib`.
- It executes test functions, safely catches `AssertionError` exceptions, and generates a color-coded PASS/FAIL summary report in the terminal.
- The `tests/` directory contains unit tests achieving high coverage across all core modules (history, scanner, duplicates, organizer).

---

## Conclusion
The **Smart File Organizer** proves that complex CLI applications—complete with beautiful terminal UIs, robust transactional disk operations, and sophisticated parsing engines—can be built cleanly and efficiently utilizing only the pure Python standard library.
