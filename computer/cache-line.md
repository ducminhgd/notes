# Cache line

## What is cache line?

A **cache line** is the smallest portion of data that can be mapped into a cache. Every mapped **cache line** is associated with a **core line**, which is a corresponding region on a **backend storage**. Both the cache storage and the **backend storage** are split into blocks of the size of a **cache line**, and all the cache mappings are aligned to these blocks. The relationship between a **cache line** and a **core line** is illustrated on the picture below.

## Cache line metadata

OCF maintains small portion of metadata per each **cache line**, which contains the following information:

- *core id*,
- *core line number*,
- *valid* and *dirty* bits for every sector.

The core id determines on which **core** is the **core line** corresponding to given **cache line** located, whereas the *core line number* determines precise **core line** location on the **backend storage**. *Valid* and *dirty* bits are explained below.

### Valid and dirty bits

*Valid* and *dirty* bits define current **cache line** state. When a **cache line** is *valid* it means, that it’s mapped to a **core line** determined by a *core id* and a *core line number*. Otherwise all the other **cache line** metadata information is *invalid* and should be ignored. When a **cache line** is in *invalid* state, it may be used to map **core line** accessed by I/O request, and then it becomes *valid*. There are few situations in which a **cache line** can return to *invalid* state:

- When the **cache line** is being evicted.
- When the core pointed to by the *core id* is being removed.
- When the core pointed to by the *core id* is being purged.
- When the entire cache is being purged.
- During discard operation being performed on the corresponding **core line**.
- During I/O request when a cache mode which may perform an invalidation is selected. Currently these cache modes are Write-Invalidate and Pass-Through.

The dirty bit determines if the **cache line** data stored in the cache is in sync with the corresponding data on the **backend storage**. If a **cache line** is dirty, then only data on the cache storage is up to date, and it will need to be flushed at some point in the future (after that it will be marked as clean by zeroing dirty bit). A **cache line** can become dirty only during write operation in Write-Back mode.

Valid and dirty bits are maintained per sector, which means that not every sector in a valid **cache line** has to be valid, as well as not every sector in a dirty **cache line** has to be dirty. An entire **cache line** is considered valid if at least one of its sectors is valid, and similarly it’s considered dirty if at least one of its sectors is dirty.

## Read more

1. [https://open-cas.github.io/cache_line.html](https://open-cas.github.io/cache_line.html)
2. [[False sharing]]
3. [A go alignment](https://go-review.googlesource.com/c/go/+/414214)
