# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.0.3] - 2026-07-23

### Added

- `DelayTimer`: a Go `time.Timer`-style one-shot timer. `new_delay_timer(duration_ns i64)` starts it immediately and fires once by sending the fire timestamp on its own unbuffered channel `c`. Supports `stop()` and `reset()` with the same true/false return contracts as Go's `Timer.Stop()`/`Timer.Reset()`. Durations are plain `i64` nanoseconds; `time.Duration` does not appear in the public API. Cancellation is race-free via a mutex-guarded generation counter.

## [0.0.2] - 2026-04-29

### Fixed

- Windows tcc/gcc builds failed with `incompatible types for redefinition of 'QueryPerformanceCounter'`. The duplicate `extern` emitted from vtimer's `fn C.QueryPerformanceCounter()` declaration conflicted with `LARGE_INTEGER *` from `windows.h`, which is pulled in transitively by V stdlib's `os` module. Resolved by removing vtimer's local declarations and relying on V stdlib `time` module's declarations (vtimer already imports `time` for `sleep`). MSVC was unaffected.
- `format_time` rejected by stricter V checker: `math.fmod(remaining, billionth)` failed with `cannot use 'i64' as 'f64' in argument 2 to 'math.fmod'`. Now casts `billionth` to `f64` explicitly.
- `timer_test.v` declared `module main` but did not import `vtimer`, leaving `new_timer`, `now_ns`, and `format_time` unresolved. Switched to `module vtimer` so tests run in-module.

### Changed

- Windows-specific `#flag windows -lKernel32` moved into a new `timer_windows.c.v` file. No public API changes.

## [0.0.1]

- Initial release: cross-platform high-precision timer with nanosecond accuracy on Windows (QueryPerformanceCounter), macOS (mach_absolute_time), and Linux (clock_gettime).
