# Tuning startup time
Using `vmtouch` to pre-load `.war` and deployed files into the OS cache can indeed minimize disk access and substantially speed up JVM startup, especially in environments where cold starts are a concern.

**Beyond OS-level tricks like `vmtouch`, here are some JVM options and strategies specifically to limit JVM startup time:**

---

### 1. **Tiered Compilation and JIT Settings**
- `-XX:+TieredCompilation` (default in most modern JVMs): Balances fast startup with peak performance by using both C1 (client) and C2 (server) JIT compilers.
- `-XX:TieredStopAtLevel=1`: Stops at the C1 (client) compiler for even faster startup, sacrificing some peak performance.
- `-Xshare:off`, `-Xshare:auto`, or `-Xshare:on`: Controls usage of the shared class data archive (see below).

### 2. **Class Data Sharing (CDS)**
- `-Xshare:auto` or `-Xshare:on`: Use shared class data archives to reduce class loading and JIT compilation time.
- `-XX:SharedArchiveFile=<path>`: Specify a CDS archive file generated with `-Xshare:dump`.

### 3. **Heap and GC Tuning for Startup**
- Use a smaller heap: `-Xms<size> -Xmx<size>` (e.g. `-Xms256m -Xmx256m`) so the JVM spends less time allocating memory at startup.
- For short-lived processes, consider `-XX:+UseSerialGC` (simpler, faster for small heaps and quick startup).

### 4. **Disable Unnecessary Features**
- `-Djava.security.egd=file:/dev/urandom` or `-Djava.security.egd=file:/dev/random` (on Linux): Prevents blocking on entropy collection for secure random generation, which can delay startup.
- Remove unnecessary agents, profilers, or debugging tools from startup flags.

### 5. **AOT (Ahead-of-Time) Compilation (Java 9+)**
- `jaotc` tool can compile classes ahead of time to native code, reducing JIT time at startup (not widely used, but can help in some use cases).

### 6. **GraalVM Native Image** (if possible)
- Compiling your app to a native image (with GraalVM) can reduce startup to milliseconds, but comes with trade-offs (e.g., limited dynamic features).

---

#### **Summary Table**

| Option/Technique                               | Purpose                              |
|------------------------------------------------|--------------------------------------|
| `-XX:+TieredCompilation` (default)             | Faster startup, then optimize        |
| `-XX:TieredStopAtLevel=1`                      | Fastest startup, less optimization   |
| Class Data Sharing (`-Xshare:on`)              | Faster class loading                 |
| Small initial/max heap (`-Xms`, `-Xmx`)        | Less memory allocation at startup    |
| `-XX:+UseSerialGC`                             | Simple GC for fast startup           |
| `-Djava.security.egd=file:/dev/urandom`        | Avoid entropy blocking               |
| Remove agents/profilers                        | Reduce JVM initialization time       |
| AOT compilation (`jaotc`)                      | Less JIT at startup                  |
| GraalVM Native Image                           | Sub-second startup                   |

---

**Tip:** The best combination depends on your app and environment. For many web apps, combining OS-level page cache preloading (like `vmtouch`) with JVM flags like `-XX:TieredStopAtLevel=1` and CDS yields substantial improvements.
