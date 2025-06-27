# RPZ-Blocklists
```
   ____  ____  __________ 
  |  _ \|  _ \|___  /  _ \ 
  | |_) | |_) |  / /| |_) | 
  |  _ <|  _ <  / / |  _ < 
  |_| \_\_| \_\/___|_| \_\ 
```


![Build Status](https://github.com/twitOne/RPZ-Blocklists/actions/workflows/update-rpz.yml/badge.svg)
![Contributors](https://img.shields.io/github/contributors/twitOne/RPZ-Blocklists)
![Code Quality](https://img.shields.io/badge/code%20quality-perl%20script-lightgrey)
[![Sponsor](https://img.shields.io/badge/Sponsor-GitHub%20Sponsors-blueviolet)](https://github.com/sponsors/twitOne)

Multi-Source RPZ Blocklists for DNS Filtering (automated conversion and updates)

This repository provides categorized, automatically updated Response Policy Zone (RPZ) blocklists from various free sources. The goal is to offer an easy-to-use, high-quality collection for DNS filtering with Unbound or any RPZ-compatible resolver, enhanced with robust change detection and validation.

## Features

- **Automated Updates**: Hourly updates via GitHub Actions, fetching and converting blocklists from `tools/urllist.txt`.
- **Categorized Blocklists**: Organized into `ads/`, `malware/`, `phishing/`, `social/`, `tracking/`, and `misc/` for easy integration.
- **Change Detection**: Uses ETag, Last-Modified, and SHA256 hash checks to skip unchanged sources, reducing unnecessary downloads.
- **Validation**: Ensures RPZ files are syntactically correct and contain valid domains.
- **SOURCES.md**: Auto-generated overview of all sources with stats (entries, size, last updated, status).
- **Robust Error Handling**: Logs failed or unreachable sources to `tools/logs/error.log` and creates GitHub issues for outdated/failed sources.
- **Customizable**: Supports wildcards (`*.<domain>`), custom filenames via `tools/list-mappings.csv`, and debug levels.
- **Performance**: Efficient processing with cached Perl modules and retry logic for failed sources.
- **License Clarity**: Includes license information in RPZ file headers, sourced from `tools/list-mappings.csv`.

## Repository Structure

```text
PZ-Blocklists/
├── ads/                        # RPZ files for ad and tracker domains
├── malware/                    # RPZ files for malware and malicious domains
├── phishing/                   # RPZ files for phishing and fraud domains
├── social/                     # RPZ files for social media domains
├── tracking/                   # RPZ files for tracking and spyware domains
├── misc/                       # RPZ files for mixed or uncategorized sources
├── tools/
│   ├── blocklist2rpz-multi.pl  # Perl script to convert and validate blocklists
│   ├── cpanfile                # Perl module dependencies
│   ├── list-mappings.csv       # Maps URLs to categories, filenames, and licenses
│   ├── source-hashes.csv       # Tracks source hashes, ETags, and stats
│   ├── urllist.txt             # List of blocklist sources (<category>,<url>)
│   ├── logs/                   # (Git-ignored) error, status, and validation logs
│   └── LICENSE                 # GPLv3 license for the conversion script
├── .github/workflows/
│   └── update-rpz.yml          # GitHub Actions workflow for hourly updates
├── .gitignore                  # Ignores logs and temporary files
├── README.md                   # Project documentation
├── CONTRIBUTING.md             # Contribution guidelines
├── git-guide.md                # Git workflow guide
└── SOURCES.md                  # Auto-generated source overview
```

## How to Use

Edit Blocklist Sources:

- Edit tools/urllist.txt to add or remove sources in <category>,<url> format (e.g., ads,https://example.org/hosts.txt).
- Map sources to custom filenames and licenses in tools/list-mappings.csv (format: `<url>,<category>,<filename>,<comments>`).
- Add new categories to RPZ_DIRS in .github/workflows/update-rpz.yml.

- Categories determine the output subdirectory, which can be extended under `update-rpz.yml` . search for 
```bash
env:
  RPZ_DIRS: ads malware misc phishing social tracking # Supported categories, add here new categories if needed
```
- Add new categories under `RPZ_DIRS:` in update-rpz.yml

Convert Lists to RPZ:
Use the Perl script to fetch, convert, and validate blocklists. Example:

```bash
perl tools/blocklist2rpz-multi.pl \
  -w \
  -d . \
  -l tools/urllist.txt \
  -m tools/list-mappings.csv \
  -e tools/logs/error.log \
  -s tools/logs/status.txt \
  --validate \
  --validation-report tools/logs/validation.txt \
  --debug-level=1
```

Options:
-  `-w`: Enable wildcard entries (*.<domain> CNAME .).
-  `-d <dir>`: Output base directory (subfolders created per category).
-  `-l <file>`: Source list (urllist.txt).
-  `-m <file>`: Mapping file (list-mappings.csv).
-  `-e <file>`: Error log file.
-  `-s <file>`: Status report file.
-  `--validate`: Validate RPZ files for syntax and domain errors.
-  `--validation-report <file>`: Write validation report.
-  `--debug-level <0|1|2>`: Debug level (0=none, 1=info, 2=full).
-  Run `perl tools/blocklist2rpz-multi.pl --help` for more options.

## Integrate with Unbound (or similar):

The generated .rpz files can be included in your DNS resolver configuration with a standard configuration like this example
```bash
rpz:
    name:                   twitOne.rpz
    zonefile:               /etc/unbound/zonefiles/twitOne.rpz
    url:                    https://raw.githubusercontent.com/twitOne/RPZ-Blocklists/main/ads/ads_example.rpz
    rpz-action-override:    nxdomain
    rpz-log:                yes
    rpz-log-name:           twitOne
    rpz-signal-nxdomain-ra: yes

``` 

## Current Categories

- **ads:** Ad and tracker domains
- **malware:** Malware, botnets, exploit domains
- **phishing:** Phishing and fraud domains
- **social:** Social media domains
- **tracking:** Tracking, spyware, telemetry
- **misc:** Mixed or uncategorized sources

## Automation with GitHub Actions

This repository includes a GitHub Actions workflow that automatically updates all RPZ blocklists hourly.

The workflow:

- File: [.github/workflows/update-rpz.yml](.github/workflows/update-rpz.yml) .
- Trigger: Hourly cron (0 * * * *) or manual via workflow_dispatch
- Checks out the repository.
- Installs Perl 5.36 and required CPAN modules (LWP::UserAgent, LWP::Protocol::https, IO::Socket::SSL, Text::CSV).
- Caches CPAN modules to speed up builds.
- Generates RPZ files using blocklist2rpz-multi.pl based on tools/urllist.txt and tools/list-mappings.csv.
- Validates generated RPZ files and logs results to tools/logs/.
- Commits and pushes updated .rpz files to the main branch.
- Logs: Available as rpz-logs artifact (status, validation, error logs).
- Manual Runs: Trigger via the GitHub Actions tab.

*Tip: Locally generated RPZ files are not required; the workflow will always generate fresh lists on the server.*

## Logging and .gitignore

- All logs (error, status, validation) are stored in tools/logs/ and excluded from the repository via .gitignore.
- This keeps the repository clean and avoids committing large or sensitive log files.

## Notes

- Blocklists are updated and validated regularly.
- All tools and configuration files are located in the tools/ directory.
- Failed or unreachable sources are logged automatically.
- The script warns if a GitHub HTML URL is detected (use RAW URLs!).

## Data Privacy

This project complies with GDPR and does not process personal data. All blocklists are publicly available and used for security purposes.

## How to Contribute

If you want to suggest new blocklist sources, report bugs, or help improve this project:

- Please read our **[Contribution Guidelines](CONTRIBUTING.md)** for details on submitting issues, feature requests, or pull requests.
- For best practices and recommended Git workflows, check the **[Git Guide](git-guide.md)**.

To add new blocklist sources, edit `tools/urllist.txt` and map them in `tools/list-mappings.csv`.  
Test your changes locally using:
```bash
perl tools/blocklist2rpz-multi.pl -w -d ./ -l tools/urllist.txt -m tools/list-mappings.csv
```

Contributions and suggestions for new high-quality sources are welcome!  
Feel free to open an issue or pull request.

## Licenses

Each RPZ file includes its license information in the header, sourced from [tools/list-mappings.csv](tools/list-mappings.csv). The conversion script (blocklist2rpz-multi.pl) is licensed under GPLv3 (see [tools/LICENSE](tools/LICENSE)).

## Special Thanks

- To all blocklist maintainers for their open-source contributions
- The open-source community for keeping the internet safer