# Security Policy

## Supported Versions

Currently, the following versions of Smart File Organizer (SFO) are supported with security updates.

| Version | Supported          |
| ------- | ------------------ |
| 1.0.x   | :white_check_mark: |
| < 1.0   | :x:                |

## Reporting a Vulnerability

If you discover a security vulnerability in SFO, please report it privately. Do not open public issues for security vulnerabilities.

Please use GitHub's private vulnerability reporting feature to submit the details of the vulnerability, including reproducible steps.

## Filesystem Safety Guarantees

SFO is designed as a filesystem manipulation tool. As such, it adheres strictly to the following safety invariants:
1. **Data Preservation**: SFO is designed to avoid unintended overwrites and to fail safely when filesystem operations cannot be completed. When a rename cannot be performed because the source and destination are on different filesystems (EXDEV), SFO uses an appropriate shutil.move fallback while retaining collision protection and recording history only after successful completion.
2. **Path Traversal Protection**: Any rule or destination path that attempts to escape the root target directory via `..` or absolute pathing will be blocked aggressively.
3. **Locking Mechanism**: A `.sfo_lock` PID file prevents concurrent executions from mutating the same directory structure simultaneously.
4. **Symlink and Hidden Path Security**:
   Symbolic links are not followed by default. The
   `follow_symlinks` configuration option can explicitly
   enable traversal where supported. Hidden files and
   directories are skipped by default.
5. **History Corruption Protection**:
   To prevent malicious or malformed filenames (e.g., containing `|` or `\n`) from corrupting the `.sfo_history` transaction log, SFO utilizes standard Base64 encoding during serialization to guarantee collision-free restoration during the undo process.
