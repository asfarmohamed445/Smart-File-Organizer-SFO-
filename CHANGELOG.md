# Changelog

All notable changes to the Smart File Organizer (SFO) project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - Production Hardened
### Added
- **Test Suite**: Comprehensive custom test suite (`sfo/test_runner.py`) covering end-to-end integration and edge cases.
- **Security**: Path traversal protection and robust validation on `SFOfile` configurations.
- **Concurrency Protection**: Custom PID-based directory locking mechanism (`.sfo_lock`) for concurrent execution protection.
- **Terminal Rendering**: Platform-agnostic console output with fallback Unicode encoding in `terminal.py`.
- **Benchmarking**: Benchmark suite `tests/benchmark.py` for performance analysis.
- **Documentation**: Added `SECURITY.md`, `THIRD_PARTY_LICENSES.md`, and comprehensive project documentation.

### Changed
- **Duplicate Detection Algorithm**: Replaced custom XOR hash with a robust, 64KB chunked `hashlib.sha256` implementation in `duplicates.py` to handle large files safely.
- **Exit Codes**: Standardized CLI exit codes (`EXIT_SUCCESS`, `EXIT_ARGS`, `EXIT_FS`, `EXIT_LOCK`, etc.) to enforce proper integration pipelines and script automation.
- **Typing**: Switched to complete structural typing throughout the project using the native `typing` module.

### Fixed
- Fixed race conditions during simultaneous `organize` calls on the same directory.
- Prevented potential data loss on identical filenames across partitions by implementing safe incremental renaming and a safe `shutil.move` fallback with collision protection.
- Corrected unreadable file handling (`PermissionError`) during duplication scanning.

---

## [0.1.0] - Initial Release
### Added
- **Core Architecture**:
  - `sfo/models.py`: Built the `FileItem` foundational data structure for file metadata tracking.
  - `sfo/scanner.py`: Built robust directory walker using `os.scandir` to detect hidden files and bypass OS-level locks.
- **Execution & Safety Engine**:
  - `sfo/organizer.py`: Created Planner-Executor pattern. Organization defaults to a dry-run to ensure safety before executing moves.
  - `sfo/filesystem.py`: Created robust move capabilities using the standard library.
- **History & Undo System**:
  - `sfo/history.py`: Implemented transaction logging into a flat, pipe-delimited `.sfo_history` file.
  - Added the `undo` command to reverse accidental operations instantly.
- **Custom Terminal UI**:
  - `sfo/terminal.py`: Built a robust zero-dependency rendering engine for colorful text, dynamic ASCII tables, and single-line progress bars.
- **Custom Configuration & Rules**:
  - `sfo/config.py` & `sfo/classifier.py`: Created an INI-style custom parser for per-directory `SFOfile` rules overriding default categories.
- **CLI Router**:
  - `sfo/cli.py`: Created native `sys.argv` command router (replacing `click`/`argparse`) for `scan`, `classify`, `organize`, `analyze`, `duplicates`, and `undo`.
- **Storage Analytics**:
  - `sfo/analyzer.py`: Built module to aggregate and format human-readable storage consumption per category.
- **Packaging**:
  - `pyproject.toml`: Added global entrypoints and metadata, packaged using `hatchling`.
