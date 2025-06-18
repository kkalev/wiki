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

----

# Creating the shared class data archive
You **do not** run your actual application (e.g., your CAS WAR) with `-Xshare:dump`.  
Instead, you run the **JVM alone** with `-Xshare:dump` to generate the shared class data archive (CDS).

**How it works:**

- The `-Xshare:dump` option tells the JVM to load all core classes and create a class data sharing archive, then exit. It does **not** start your application.

- **Typical command to create the default CDS archive:**
  ```sh
  java -Xshare:dump
  ```
  This will generate (or regenerate) the archive, usually at `$JAVA_HOME/lib/server/classes.jsa`.

- **To create a custom archive for your app’s classes** (advanced, e.g., with AppCDS), you would use additional options (like `-XX:SharedArchiveFile` and a class list), but this is optional for most setups.

---

**Summary:**  
- **You run the JVM with `-Xshare:dump` to create the archive—no application is run.**
- **Later**, when running your CAS application, you use `-Xshare:on` (and `-XX:SharedArchiveFile=<path>` if using a custom archive) to take advantage of the archive for faster startup.

Let me know if you want a full example workflow for generating and using a custom CDS archive for your CAS Docker setup!

----
# Specific tuning for Apereo CAS
Here’s a targeted summary of JVM options and strategies to **minimize startup time** for Apereo CAS 6.6.x on Java 11 in Docker:

---

## Recommended JVM Options for Fast Startup

### 1. **Tiered Compilation**
- **Faster startup, some trade-off in peak performance:**
  - `-XX:TieredStopAtLevel=1`  
    (Stops at C1 compiler, makes JIT compilation lightweight and startup faster.)
- **Default tiered compilation (good compromise):**
  - Java 11 enables `-XX:+TieredCompilation` by default.  
    (No need to specify unless you’ve disabled it.)

### 2. **Heap Sizing**
- **Smaller initial heap = less time to allocate on startup:**
  - `-Xms256m -Xmx512m`  
    (Adjust based on your CAS and expected load. For just fast startup, keep these small.)

### 3. **Garbage Collection**
- **Simple GC for fast startup in small/medium heaps:**
  - `-XX:+UseSerialGC`  
    (Low-overhead GC, good for startup speed. If your heap is larger, consider G1GC but SerialGC is fastest for small heaps.)

### 4. **Class Data Sharing (CDS)**
- **Leverage JVM’s CDS to accelerate class loading:**
  - **Create a shared archive** (one-time setup, then reuse in Docker):
    1. Run your app once with:  
       `-Xshare:dump`
    2. Use the generated archive in your container:
       - `-Xshare:on -XX:SharedArchiveFile=/path/to/archive`

### 5. **Entropy Source for Random**
- **Avoid blocking on SecureRandom at startup:**
  - `-Djava.security.egd=file:/dev/urandom`

### 6. **Remove Unused Features**
- Avoid loading unnecessary agents, debuggers, or profilers unless needed.

---

## Example JVM Startup Command

```sh
java \
  -XX:TieredStopAtLevel=1 \
  -Xms256m -Xmx512m \
  -XX:+UseSerialGC \
  -Djava.security.egd=file:/dev/urandom \
  -Xshare:on \
  -XX:SharedArchiveFile=/cas-cds.jsa \
  -jar cas.war
```

- Adjust `-Xms`/`-Xmx` for your real workload after measuring.
- Replace `/cas-cds.jsa` with your actual CDS archive path.

---

## Docker Tips

- **Layer your WAR and dependencies early in your Dockerfile** to maximize image caching.
- **Use `vmtouch`** as you mentioned, to pre-warm the image if disk I/O is a bottleneck (especially on cold starts).
- **Keep your container base image minimal** (e.g., `openjdk:11-jre-slim`).

---
