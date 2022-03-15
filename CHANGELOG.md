# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.6.5] - 2022-03-13
### Added
- Add instructions for installing [rustypaste](https://archlinux.org/packages/community/x86_64/rustypaste/) on Arch Linux
  - `pacman -S rustypaste` 🎉

### Fixed
- Fix a bug where the use of `CONFIG` environment variable causes a conflict between the configuration file path and `[config]` section

## [0.6.4] - 2022-03-11
### Added
- Support setting the refresh rate for hot-reloading the configuration file.

```toml
[config]
refresh_rate="1s"
```

- Support setting the timeout for HTTP requests.

```toml
[server]
timeout="30s"
```

### Security
- Bump [regex crate](https://github.com/rust-lang/regex) to **1.5.5**
  - Fixes [CVE-2022-24713](https://github.com/advisories/GHSA-m5pq-gvj9-9vr8)

## [0.6.3] - 2022-02-24
### Added
- Support setting the authentication token in the configuration file.
  - This is an alternative (but not recommended) way of setting up authentication when the use of `AUTH_TOKEN` environment variable is not applicable.

```toml
[server]
auth_token="hunter2"
```

## [0.6.2] - 2021-12-05
### Updated
- Improve the concurrency
  - Shrink the scope of non-suspendable types (`#[must_not_suspend]`) for dropping them before reaching a suspend point (`.await` call). This avoids possible deadlocks, delays, and situations where `Future`s not implementing `Send`.
  - Reference: https://rust-lang.github.io/rfcs/3014-must-not-suspend-lint.html

## [0.6.1] - 2021-11-16
### Fixed
- Gracefully handle the hot-reloading errors.
  - Errors that may occur while locking the [Mutex](https://doc.rust-lang.org/std/sync/struct.Mutex.html) are handled properly hence a single configuration change cannot take down the whole service due to [poisoning](https://doc.rust-lang.org/std/sync/struct.Mutex.html#poisoning).

## [0.6.0] - 2021-11-07
### Added
- Support pasting files from remote URLs (via `remote=` form field)
  - `{server.max_content_length}` is used for download limit
  - See [README.md#paste-file-from-remote-url](https://github.com/orhun/rustypaste#paste-file-from-remote-url)

- Hot reload configuration file to apply configuration changes instantly without restarting the server

### Changed
- Library: Switch to Rust 2021 edition

### Security
- Prevent serving an already expired file

In the previous versions, it was possible to view an expired file by using the correct extension (timestamp). e.g. `paste.com/expired_file.txt.1630094518049` will serve the file normally although `paste.com/expired_file.txt` says that it is expired. This version fixes this vulnerability by regex-checking the requested file's extension.

reference: [f078a9afa74f8608ee3f2a6e705159df15915c78](https://github.com/orhun/rustypaste/commit/f078a9afa74f8608ee3f2a6e705159df15915c78)

## [0.5.0] - 2021-10-12
### Added
- Added an entry in the configuration file to disable "duplicate uploads":

```toml
[paste]
# default: true
duplicate_files = false
```

Under the hood, it checks the SHA256 digest of the uploaded files.

## [0.4.1] - 2021-09-19
### Changed
- Update README.md:
  - Mention the new standalone tool: [rustypaste-cli](https://github.com/orhun/rustypaste-cli)
  - Add [installation](https://github.com/orhun/rustypaste#installation) section.

## [0.4.0] - 2021-08-27
### Added
- Support [expiring links](README.md#expiration) (via `expire:` header)
  - Timestamps are used as extension for expiring files
  - Expired files can be cleaned up with [this command](README.md#cleaning-up-expired-files)
- Support [one shot links](README.md#one-shot) (via `oneshot=` form field)
  - `{server.upload_path}/oneshot` is used for storage

## [0.3.1] - 2021-08-10
### Fixed
- Switch to [upload-release-action](https://github.com/svenstaro/upload-release-action) for uploading releases

## [0.3.0] - 2021-08-09
### Added
- Support overriding MIME types (config: `mime_override`)
- Support blacklisting MIME types (config: `mime_blacklist`)

## [0.2.0] - 2021-08-04
### Added
- Support shortening URLs (via `url=` form field)
  - `{server.upload_path}/url` is used for storage

## [0.1.3] - 2021-07-28
### Fixed
- Prevent sending empty file name and zero bytes
- Prevent path traversal on upload directory ([#2](https://github.com/orhun/rustypaste/issues/2))
- Check the content length while reading bytes for preventing OOM ([#1](https://github.com/orhun/rustypaste/issues/1))

## [0.1.2] - 2021-07-27
### Changed
- Update Continuous Deployment workflow to publish Docker images

## [0.1.1] - 2021-07-27
Initial release.
