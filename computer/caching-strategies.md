---
type: caching
keywors: cache
---
# Caching strategies

Keywords: [[Cache]], [[Caching]]

## Cache Aside

1. If data exists in **cache**, **application** reads data from **cache**.
2. If not
   1. **Application** reads data from **database**
   2. **Application** updates data to **cache**.

## Read Through

1. If data exists in **cache**, **application** read data from **cache**.
2. If not
   1. **Cache** reads data from **database**
   2. **Cache** returns data to **application**

## Write Through

1. **Application** writes data to **cache**.
2. **Cache** writes data to **database** immediately.

## Write Back a.k.a Write Behind

1. **Application** writes data to **cache** constantly.
2. **Cache** writes data to **database** once in a while.

## Write Around

### With Cache Aside

1. **Application** writes data to **database**.
2. If data exists in **cache**, **application** reads data from **cache**.
3. If not
   1. **Application** reads data from **database**
   2. **Application** updates data to **cache**.

### With Read Through

1. **Application** writes data to **database** only.
2. If data exists in **cache**, **application** read data from **cache**.
3. If not
   1. **Cache** reads data from **database**
   2. **Cache** returns data to **application**
