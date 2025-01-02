# Multithreaded Web Server

A simple multithreaded web server implemented in Rust, from Chapter 20 of *The Rust Programming Language* book. This project demonstrates key concepts in systems programming, including thread management, message passing, and basic networking.

## Overview

The server listens on `127.0.0.1:7878` and handles incoming HTTP requests. It supports basic routes and uses a custom thread pool to manage connections efficiently.

### Key Features

- **Multithreaded Design**: Uses a custom thread pool to handle multiple client requests concurrently.
- **Basic Routing**: Responds to `GET /` with an HTML file, `GET /sleep` with a delayed response, and other routes with a 404 page.
- **Graceful Shutdown**: Cleans up threads upon server shutdown.

## Getting Started

### Prerequisites

- [Rust](https://www.rust-lang.org/tools/install)

### Running the Server

1. Clone this repository:

    ```sh
    git clone https://github.com/imaazkhalid/web_server.git
    cd web_server
    ```

2. Start the server:

    ```sh
    cargo run
    ```

3. Open a browser and navigate to `http://127.0.0.1:7878`.  

### Supported Routes

- `GET /`: Responds with the content of `hello.html`.
- `GET /sleep`: Simulates a delayed response by waiting 5 seconds before returning `hello.html`.
- Other routes: Returns a 404 response with the content of `404.html`.

## Project Structure

- **`src/main.rs`**: 
  - Handles TCP connections, parses HTTP requests, and generates responses.
  - Manages the server lifecycle and integrates the thread pool.
- **`src/lib.rs`**: 
  - Implements a custom `ThreadPool` for managing worker threads and executing jobs concurrently.

## ThreadPool Implementation

The `ThreadPool` is a simplified thread pool implementation that demonstrates:

- **Job Queueing**: Uses a channel (`mpsc::Sender` and `Receiver`) to queue tasks.
- **Worker Threads**: Each worker continuously listens for jobs and executes them.
- **Graceful Shutdown**: Ensures all threads are properly terminated during the server shutdown.

### Code Highlights

#### `ThreadPool::new`
Initializes the thread pool with a specified number of threads.

```rust
pub fn new(size: usize) -> ThreadPool {
    assert!(size > 0);
    // ... setup workers and channels ...
}
```

#### `ThreadPool::execute`
Queues a new task for execution.

```rust
pub fn execute<F>(&self, f: F)
where
    F: FnOnce() + Send + 'static,
{
    let job = Box::new(f);
    self.sender.as_ref().unwrap().send(job).unwrap();
}
```

#### Worker
Represents a thread that processes tasks from the queue.

```rust
struct Worker {
    id: usize,
    thread: Option<thread::JoinHandle<()>>,
}
```

## Learning Highlights

This project provides hands-on experience with:

- TCP networking and handling HTTP requests
- Using Rust's concurrency primitives (`thread`, `mpsc`, `Mutex`, and `Arc`)
- Implementing the Drop trait for resource cleanup
- Building and managing a thread pool

## Limitations and Future Improvements

- **Static Content Only**: Currently serves hardcoded HTML files.
- **Limited Routing**: No dynamic route handling or request parsing beyond the basics.
- **No Logging**: Could benefit from structured logging for better debugging and monitoring.
- **Scalability**: Not optimized for high-concurrency scenarios.

---

This web server is an excellent learning exercise for understanding the foundations of Rust's concurrency and networking capabilities.