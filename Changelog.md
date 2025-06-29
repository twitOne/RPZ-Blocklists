# Changelog

All notable changes to this project will be documented in this file.

---

## [1.4.1] – 2025-06-29

### Added
- Cleanup logic in `blocklist2rpz` for removed addresses

### Changed
- Remove Mandiant URL since it is too old
- Update Frogeye license
- Updated Bitbucket URL

---

## [1.4.0] – 2025-06-28

### Added
- Dynamic file size units (B, KB, MB, GB) in `SOURCES.md`
- More space for the "Size" entry in `SOURCES.md`
- Epic ASCII banner in README

### Changed
- Removed `-KB` from the `source-hashes.csv` header, updated `SOURCES.md` accordingly
- Improved Status Summary output: added separator lines and explicit initialization
- Updated documentation to version 1.4 (future updates will be tracked in the Changelog instead of script comments)

---

## [1.3.3] – 2025-06-27

### Changed
- Updated RPZ blocklists and source hashes
- Updated Frogeye source

### Removed
- Removed Hagezi phishing list as it is no longer reachable
- Removed Mandiant URL

---

## [1.3.2] – 2025-06-27

### Fixed
- Fixed cache path in workflow
- Fixed YAML syntax in `update-rpz.yml` by adding missing `path:` key
- Cosmetic improvements

### Changed
- Updated README with new features and structure

---

## [1.3.1] – 2025-06-27

### Fixed
- Fixed wide character error when writing RPZ files by using `:raw` and explicit encoding
- Fixed syntax error by removing duplicate if block and standardizing indentation

---

## [1.3.0] – 2025-06-27

### Added
- ETag/Hash change detection for blocklists in `blocklist2rpz-multi.pl`
- Hash-checking feature in `blocklist2rpz-multi.pl`
- Improved generation of `SOURCES.md`

---

## [1.2.2] – 2025-06-27

### Added
- Explicit `git add` commands in workflow for `SOURCES.md`
- Set timeout for `update-rpz.yml` to 45 minutes

---

## [1.2.1] – 2025-06-27

### Fixed
- Minor bugfixes and workflow tweaks

---

## [1.2.0] – 2025-06-27

### Added
- Introduce `Changelog.md` version with basic functionality from version 1.2

