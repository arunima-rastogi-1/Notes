    
   

## #1. What is the difference between `final`, `finally`, and `finalize()` in Java?

**Answer:**
- `final`: A keyword used with classes, methods, and variables.
  - Final variable → cannot be reassigned.
  - Final method → cannot be overridden.
  - Final class → cannot be extended.
- `finally`: A block used with `try-catch` for cleanup code. It always executes, regardless of exceptions.
- `finalize()`: A method in `Object` class called by the Garbage Collector before object destruction. Rarely used; deprecated in Java 9.

---

## #2. How does the Java memory model (JMM) handle visibility and ordering of variables in multithreading?

**Answer:**
- JMM defines how threads interact through memory.
- Without synchronization, changes in one thread may not be visible to others (due to CPU caches, reordering).
- Tools to ensure visibility:
  - `volatile` → guarantees visibility of writes across threads.
  - `synchronized` → ensures both visibility and mutual exclusion.
  - `Lock` & `Atomic` classes → modern concurrency utilities.

---

## #3. Explain how the `HashMap` works internally in Java.

**Answer:**
1. `HashMap` uses an array of buckets (Node<K,V>[]).
2. Each entry stores key, value, hash, and next reference.
3. Index = `hash(key) % capacity`.
4. Collisions resolved using linked list (Java 7) or tree (Java 8+) after threshold.
5. Load factor (default 0.75) triggers resizing when exceeded.
6. Performance: 
   - Average O(1).
   - Worst case O(n) (linked list) / O(log n) (tree).

---

## #4. What are `weak references` in Java, and when would you use them?

**Answer:**
- A `WeakReference` allows garbage collection of the referenced object if no strong references exist.
- Useful in caching, memory-sensitive data structures (e.g., `WeakHashMap`).
- Example:
  ```java
  WeakReference<MyClass> ref = new WeakReference<>(new MyClass());
