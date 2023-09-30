# rocksdb
Enhanced version of https://github.com/Level/rocksdb that patches "deps/rocksdb/rocksdb/utilities/transactions/lock/range/range_tree/lib/portability
/toku_time.h" so this can be built on more ARM devices.

```
static inline tokutime_t toku_time_now(void) {
#if defined(__x86_64__) || defined(__i386__)
  uint32_t lo, hi;
  __asm__ __volatile__("rdtsc" : "=a"(lo), "=d"(hi));
  return (uint64_t)hi << 32 | lo;
#elif defined(__aarch64__)
  uint64_t result;
  __asm __volatile__("mrs %[rt], cntvct_el0" : [ rt ] "=r"(result));
  return result;
#elif defined(__arm__) // NEW CODE
  uint32_t lo, hi; // NEW CODE
  __asm __volatile__("mrrc p15, 1, %[lo], %[hi], c14" : [ lo ] "=r" (lo), [hi] "=r" (hi)); // NEW CODE
  return (uint64_t)hi << 32 | lo; // NEW CODE
#elif defined(__powerpc__)
  return __ppc_get_timebase();
#else
#error No timer implementation for this platform
#endif
}
```
