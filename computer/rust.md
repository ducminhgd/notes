# Rust

## Rust and other languages

### [[Go]]

[Rust vs Go](https://www.getclockwise.com/blog/rust-vs-go)

- **On-par performance**

    Both Go and Rust have excellent built-in, high-performance standard build and dependency management tools. Rust will almost always beat Go in run-time benchmarks due to its fine-grained control over how threads behave and how resources are shared between threads. Both Rust and Go use a standard formatting tool—gofmt for Go and rustfmt for Rust—which rewrites your code automatically using the canonical style.

    Ultimately, Rust lets programmers get "closer to the metal" with more control (that is, the machine the code will be running on). By contrast, Go abstracts away from the architectural details to let programmers get closer to the problem they're trying to solve.

- **Ensuring security**
  
    Rust's heavy emphasis on preventing memory-related [[security]] vulnerabilities means programmers have to go out of their way to perform tasks that would be simpler in other languages, including Go. However, Go isn't [[insecure]]. Rust just happens to be king for memory safety. One of the biggest causes of bugs and security vulnerabilities is accessing memory unsafely or incorrectly. Rust offers several concurrency paradigms to help avoid common memory safety pitfalls.

- **Ease of use**
  
    Unlike interpreted languages, which tend to force developers into a particular way of approaching things, both Go and Rust are practical languages aimed at solving problems in whatever way is most appropriate for an application. It's no easy feat to guarantee memory-safe concurrency without compromising the developer experience.

    Rust and Go can both claim memory-safe concurrency and a good developer experience. Admittedly, coding with Rust is more difficult and takes longer. Overall, Rust has a steeper learning curve than Go. Additionally, the developer experience edge goes to Go when it comes to [[concurrency]].


