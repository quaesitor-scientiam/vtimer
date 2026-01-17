# vtimer

A high-precision, cross-platform timer package for the V programming language with nanosecond accuracy.

## Overview

vtimer provides atomic and precise timing functionality for V applications. It uses platform-specific high-resolution timers (QueryPerformanceCounter on Windows, mach_absolute_time on macOS, and clock_gettime on Linux/Unix) to deliver accurate timing measurements across all platforms.

## Features

- **High Precision**: Nanosecond-level accuracy using platform-specific APIs
- **Cross-Platform**: Works seamlessly on Windows, macOS, and Linux/Unix
- **Thread-Safe**: Uses atomic operations for concurrent access
- **Easy to Use**: Simple, intuitive API
- **Multiple Time Formats**: Convert to microseconds, milliseconds, seconds, minutes, hours, and days
- **Human-Readable Output**: Built-in time formatting function
- **Monotonic Clock**: Uses monotonic time sources to avoid issues with system clock adjustments

## Installation

Install vtimer using V's package manager:

```bash
v install quaesitor-scientiam.vtimer
```

Or add it to your `v.mod` file:

```v
Module {
    dependencies: ['quaesitor-scientiam.vtimer']
}
```

## Usage

### Basic Timer

```v
import quaesitor_scientiam.vtimer

fn main() {
    mut timer := vtimer.new_timer()
    
    timer.start()
    // ... do some work ...
    timer.stop()
    
    println('Elapsed time: ${timer.ns()} nanoseconds')
    println('Elapsed time: ${timer.ns_to_ms():.3f} milliseconds')
}
```

### Running Timer

```v
import quaesitor_scientiam.vtimer
import time

fn main() {
    mut timer := vtimer.new_timer()
    timer.start()
    
    // Timer keeps running
    time.sleep(100 * time.millisecond)
    
    // Get elapsed time without stopping
    println('Current elapsed: ${timer.ns_to_ms():.3f} ms')
    
    time.sleep(100 * time.millisecond)
    timer.stop()
    
    println('Final elapsed: ${timer.ns_to_ms():.3f} ms')
}
```

### Get Current Time

```v
import quaesitor_scientiam.vtimer

fn main() {
    // Get current high-resolution timestamp in nanoseconds
    current_time := vtimer.now_ns()
    println('Current time: ${current_time} ns')
}
```

### Format Time

```v
import quaesitor_scientiam.vtimer

fn main() {
    mut timer := vtimer.new_timer()
    timer.start()
    
    // ... do work ...
    
    timer.stop()
    
    // Format the elapsed time in human-readable form
    formatted := vtimer.format_time(timer.ns())
    println('Elapsed: ${formatted}')
    // Example output: "2 mins 30 secs" or "1.234 ms"
}
```

## API Reference

### Timer Struct

```v
pub struct Timer {
    freq  i64
    coeff Time_Coefficients
pub mut:
    start_t i64
    end_t   i64
    running bool
}
```

### Functions

#### `new_timer() Timer`
Creates and initializes a new Timer instance.

```v
mut timer := vtimer.new_timer()
```

#### `(mut t Timer) start()`
Starts or restarts the timer. Sets the start time to the current high-resolution timestamp.

```v
timer.start()
```

#### `(mut t Timer) stop()`
Stops the timer. Records the current timestamp as the end time.

```v
timer.stop()
```

#### `(mut t Timer) ns() i64`
Returns the elapsed time in nanoseconds.

```v
elapsed := timer.ns()
```

#### `(mut t Timer) ns_to_us() f64`
Returns the elapsed time in microseconds.

```v
elapsed_us := timer.ns_to_us()
```

#### `(mut t Timer) ns_to_ms() f64`
Returns the elapsed time in milliseconds.

```v
elapsed_ms := timer.ns_to_ms()
```

#### `(mut t Timer) ns_to_secs() f64`
Returns the elapsed time in seconds.

```v
elapsed_secs := timer.ns_to_secs()
```

#### `(mut t Timer) ns_to_mins() f64`
Returns the elapsed time in minutes.

```v
elapsed_mins := timer.ns_to_mins()
```

#### `(mut t Timer) ns_to_hrs() f64`
Returns the elapsed time in hours.

```v
elapsed_hrs := timer.ns_to_hrs()
```

#### `(mut t Timer) ns_to_days() f64`
Returns the elapsed time in days.

```v
elapsed_days := timer.ns_to_days()
```

#### `now_ns() i64`
Returns the current high-resolution timestamp in nanoseconds. Useful for custom timing operations.

```v
start := vtimer.now_ns()
// ... do work ...
end := vtimer.now_ns()
elapsed := end - start
```

#### `format_time[T](ns T) string`
Formats a nanosecond value into a human-readable string. Automatically chooses appropriate units.

```v
formatted := vtimer.format_time(123456789000)
// Returns: "2 mins 3 secs"

formatted := vtimer.format_time(1234567)
// Returns: "1.235 ms"
```

## Examples

### Benchmarking Code

```v
import quaesitor_scientiam.vtimer

fn fibonacci(n int) int {
    if n <= 1 { return n }
    return fibonacci(n - 1) + fibonacci(n - 2)
}

fn main() {
    mut timer := vtimer.new_timer()
    
    timer.start()
    result := fibonacci(30)
    timer.stop()
    
    println('Result: ${result}')
    println('Time taken: ${vtimer.format_time(timer.ns())}')
    println('Precise time: ${timer.ns_to_ms():.6f} ms')
}
```

### Performance Comparison

```v
import quaesitor_scientiam.vtimer

fn method_a() {
    mut sum := 0
    for i in 0..1000000 {
        sum += i
    }
}

fn method_b() {
    mut sum := 0
    for i in 0..1000000 {
        sum = sum + i
    }
}

fn main() {
    mut timer := vtimer.new_timer()
    
    // Test method A
    timer.start()
    method_a()
    timer.stop()
    time_a := timer.ns()
    
    // Test method B
    timer.start()
    method_b()
    timer.stop()
    time_b := timer.ns()
    
    println('Method A: ${vtimer.format_time(time_a)}')
    println('Method B: ${vtimer.format_time(time_b)}')
    println('Difference: ${(time_a - time_b).abs()} ns')
}
```

## Platform Details

### Windows
Uses `QueryPerformanceCounter` and `QueryPerformanceFrequency` for high-resolution timing.

### macOS
Uses `mach_absolute_time()` with `mach_timebase_info` for conversion to nanoseconds.

### Linux/Unix
Uses `clock_gettime()` with `CLOCK_MONOTONIC` for monotonic time measurements.

## Thread Safety

The Timer struct uses atomic operations for reading and writing start and end times, making it safe for concurrent access. Multiple threads can read elapsed time while the timer is running.

## Requirements

- V 0.3.0 or higher
- Works on Windows, macOS, and Linux/Unix systems

## Building and Testing

Build the package:

```bash
v -shared .
```

Run tests (if available):

```bash
v test .
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is open source and available under the [MIT License](LICENSE).

## Author

Richard Wheeler ([@quaesitor-scientiam](https://github.com/quaesitor-scientiam))

## Links

- [GitHub Repository](https://github.com/quaesitor-scientiam/vtimer)
- [V Language](https://vlang.io/)

## Support

If you encounter any issues or have questions, please open an issue on the [GitHub repository](https://github.com/quaesitor-scientiam/vtimer/issues).
