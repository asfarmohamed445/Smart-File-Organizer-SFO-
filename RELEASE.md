# SFO Release Checklist

Follow this checklist before cutting a new release for the Smart File Organizer (SFO).

## 1. Pre-Release Verification
- [ ] **Run all unit tests**: Execute the custom test runner to ensure no regressions.
  ```bash
  python tests/run_all.py
  ```
- [ ] **Run benchmarks**: Ensure performance metrics (specifically the duplication hashing speed) remain acceptable.
  ```bash
  python tests/benchmark.py
  ```
- [ ] **Manual Sanity Check**: Run a dry-run against a complex directory to visually verify terminal output.
  ```bash
  sfo organize ./path/to/test_dir
  ```

## 2. Version Bumps and Documentation
- [ ] **Update Version**: Bump the version number in `pyproject.toml` according to [Semantic Versioning](https://semver.org/).
- [ ] **Update CHANGELOG.md**:
  - Move changes from the `Unreleased` section (if any) to a new version block `[X.Y.Z] - YYYY-MM-DD`.
  - Ensure all notable additions, fixes, and security patches are properly documented.
- [ ] **Update Documentation**: Ensure `README.md`, `SECURITY.md`, and `SFO_DETAILED_DOCUMENTATION.md` reflect any newly added commands or configuration options.

## 3. Git Operations
- [ ] **Commit Changes**:
  ```bash
  git add pyproject.toml CHANGELOG.md
  git commit -m "chore: bump version to X.Y.Z"
  ```
- [ ] **Tag the Release**:
  ```bash
  git tag -a vX.Y.Z -m "Release vX.Y.Z"
  git push origin main --tags
  ```

## 4. Build and Publish
- [ ] **Build the Package**: Use `hatchling` via the build frontend.
  ```bash
  python -m build
  ```
- [ ] **Publish to PyPI**:
  ```bash
  twine upload dist/*
  ```
- [ ] **Verify Install**: Install the new version globally in a clean environment to ensure entrypoints resolve properly.
  ```bash
  pip install sfo
  sfo --version
  ```

## 5. Post-Release
- [ ] Create a GitHub Release referencing the new tag.
- [ ] Copy the relevant section of `CHANGELOG.md` into the GitHub Release description.
