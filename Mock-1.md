| Topic                        | Key Understanding                                                                                                              |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| **Data Types in Java**       | Know `byte` (1B), `short` (2B), `int` (4B), `long` (8B), `float` (4B), `double` (8B), `char` (2B), `boolean` (\~1B internally) |
| **ArrayList**                | Backed by array, resizes via `grow()` (1.5x), random access O(1), insert/delete O(n)                                           |
| **Amortized Time**           | Explains average time over operations; ArrayList `.add()` is O(1) amortized                                                    |
| **LinkedList vs Array**      | Array: O(1) access, slow insert/delete; LinkedList: O(n) access, fast insert/delete at ends                                    |
| **Collision Handling**       | Linear chaining (List of nodes), probing (linear/quadratic), double hashing (2 hash functions)                                 |
| **Hash for String**          | Polynomial Rolling Hash (used in Java): `s[0]*31ⁿ⁻¹ + s[1]*31ⁿ⁻² + ...`                                                        |
| **% 1000 vs .hashCode()**    | `.hashCode()` can be large; `% 1000` restricts to table size. Better: `(h ^ (h >>> 16)) % size`                                |
| **Nested Array for HashSet** | Used for primitive-safe space-saving in buckets                                                                                |
| **LRU Cache**                | Use `LinkedHashMap` or custom: HashMap + Doubly LinkedList (O(1) ops)                                                          |

Perfect! Let’s walk through **Phase 1: Recap and Deep Understanding** step by step. This will solidify your conceptual base before we move on to mock practice.

---

## ✅ Phase 1: Recap and Deep Understanding (Expanded)

---

### 🔹 1. **Data Types in Java and Their Sizes**

| Type      | Size                     | Range / Notes                                       |
| --------- | ------------------------ | --------------------------------------------------- |
| `byte`    | 1 byte                   | -128 to 127                                         |
| `short`   | 2 bytes                  | -32,768 to 32,767                                   |
| `int`     | 4 bytes                  | -2³¹ to 2³¹-1                                       |
| `long`    | 8 bytes                  | -2⁶³ to 2⁶³-1                                       |
| `float`   | 4 bytes                  | Single-precision 32-bit IEEE 754                    |
| `double`  | 8 bytes                  | Double-precision 64-bit IEEE 754                    |
| `char`    | 2 bytes                  | 0 to 65535 (Unicode characters)                     |
| `boolean` | JVM-dependent (\~1 byte) | Only `true` or `false` (stored compactly in arrays) |

👉 Know where overflow happens (e.g., `int` max + 1 = negative value due to wrap-around).

---

### 🔹 2. **ArrayList Internal Implementation**

* Backed by a **resizable array**
* Default capacity: **10**
* On capacity overflow: **grows by 1.5x** (`newCapacity = old + (old >> 1)`)
* `add()` is **O(1)** *amortized*, but **O(n)** when resize happens.
* Access by index: **O(1)**
* Insert/delete at arbitrary index: **O(n)** (shifting)

```java
ArrayList<Integer> list = new ArrayList<>();
```

---

### 🔹 3. **Amortized Time Complexity**

* Not every operation takes the same time.
* For example, `ArrayList.add()` is usually **O(1)**, but when resizing happens, it takes **O(n)** to copy elements.
* However, over many operations, the average time is still **O(1)** — this is **amortized** complexity.

💡 Example:

```java
for (int i = 0; i < 10000; i++) {
    list.add(i);  // most are O(1), a few are O(n) due to resizing
}
```

---

### 🔹 4. **Collision Handling in Hashing**

* **Separate Chaining**: Store a list/linked list at each bucket
* **Linear Probing**: If a spot is taken, go to the next one (wrap around)
* **Quadratic Probing**: Try `i^2` jumps
* **Double Hashing**: Use a second hash function for jump size

```java
index = (h1(key) + i * h2(key)) % table_size
```

* **Binary Chaining**: Some systems store hash buckets in binary trees (Java HashMap does this if collisions ≥ 8)

---

### 🔹 5. **Double Hashing with Code**

Example (with open addressing):

```java
int h1 = key % size;
int h2 = PRIME - (key % PRIME);
int index = (h1 + i * h2) % size;
```

Use when:

* You want to avoid clustering (linear probing causes clusters).
* Second hash function must never evaluate to 0.

---

### 🔹 6. **Nested Array in Array for HashSet Buckets**

Used to store bits for primitive-safe 2D hash storage:

```java
boolean[][] hash = new boolean[1000][1000];
```

* First level hashes with `key % 1000`
* Second level with `key / 1000`

Java uses this trick in some implementations like `MyHashSet` for memory efficiency.

---

### 🔹 7. **How to Hash Strings in Java**

Java’s `String.hashCode()` uses:

```java
s[0]*31ⁿ⁻¹ + s[1]*31ⁿ⁻² + ... + s[n-1]
```

* 31 is a small prime → avoids overflow and distributes bits
* Hash is `int` (can overflow, that’s okay in Java)

Example:

```java
"abc".hashCode() = 97×31² + 98×31 + 99 = 96354
```

---

### 🔹 8. **LinkedList vs Array**

| Feature        | Array             | LinkedList        |
| -------------- | ----------------- | ----------------- |
| Access         | O(1)              | O(n)              |
| Insert/delete  | O(n)              | O(1) at head/tail |
| Memory usage   | Less (contiguous) | More (pointers)   |
| Cache-friendly | Yes               | No                |

Arrays are better for **random access**, linked lists for **frequent insert/delete** at ends.

---

### 🔹 9. **HashMap, Stack, Queue: All Implementations**

| DS      | Backing Options                 | Pros                      | Time Complexity      |
| ------- | ------------------------------- | ------------------------- | -------------------- |
| HashMap | Array of LinkedLists / Tree     | Fast access O(1) avg      | O(1) avg, O(n) worst |
| Stack   | Array / LinkedList              | LIFO, easy implementation | O(1) push/pop        |
| Queue   | Array / LinkedList / Two stacks | FIFO, flexible            | O(1) enqueue/dequeue |

---

### 🔹 10. **LRU Cache (ArrayList vs LinkedList)**

| Feature            | LinkedList + HashMap               | ArrayList           |
| ------------------ | ---------------------------------- | ------------------- |
| Time Complexity    | O(1) for get/put via HashMap + DLL | O(n) for get/remove |
| Preferred Approach | Doubly Linked List + HashMap       | Not ideal for LRU   |

* **LinkedList** allows O(1) removal from both ends.
* **ArrayList** requires O(n) for removing from the middle.

---

### 🔹 11. **Mod 1000 vs hashCode() Difference**

* `key % 1000`: compresses value to range 0–999
* `.hashCode()`: provides a high-entropy integer
* Always use: `hashCode()` → spread → `% size`

```java
int h = key.hashCode();
h = h ^ (h >>> 16);  // hash spreading
index = h % tableSize;
```

---

### 🔹 12. **Handling 10⁶+ Elements**

* Avoid using `ArrayList` or structures that grow linearly unless amortized is acceptable
* Hash-based structures or segment trees, tries, heaps are preferred
* Pre-allocate where possible to avoid repeated resizing

---

### 🔹 13. **How to Ask Clarifying Questions in Coding Interviews**

Before writing code, ask:

* “Are there any constraints on time/space complexity?”
* “Can the input contain negative numbers or duplicates?”
* “Is the input always valid or do I need to handle edge cases?”
* “Should I return one solution or all?”
* “Can I use additional data structures or libraries?”

This shows **thoughtfulness and problem scoping**.

---

### 🔹 14. **Pros and Cons of Common DS**

| DS         | Pros                             | Cons                                 |
| ---------- | -------------------------------- | ------------------------------------ |
| Array      | Fast access O(1), compact memory | Fixed size, costly insert/delete     |
| LinkedList | Dynamic size, fast insert/delete | Slow access, more memory usage       |
| ArrayList  | Resizable, fast access           | Insert/delete is O(n), resizing cost |
| Stack      | Simple, LIFO                     | Not flexible for random access       |
| Queue      | FIFO, flexible                   | Use circular buffer for efficiency   |
| HashMap    | Fast lookups O(1)                | Collisions, non-deterministic order  |

---

### 🔹 15. **Queue Using Two Stacks**

| Method                | Description                      | Time Complexity |
| --------------------- | -------------------------------- | --------------- |
| Two Stacks            | One for enqueue, one for dequeue | O(1) amortized  |
| One Stack + Recursion | More clever, less intuitive      | O(n) per op     |

Standard way is **two stacks**, for simplicity and clarity.

---

### 🔹 16. **Rotated Sorted Array Logic**

* Key idea: **one half is always sorted**
* Compare `nums[mid]` with `nums[left]` and `nums[right]` to decide direction
* Helps find:

  * Min value
  * Target value

Great catch — let me explain what I meant by:

> **"Use circular buffer for efficiency"** (under Queue in the DS table)

---

### 🎯 **What is a Circular Buffer?**

A **circular buffer** (also called a **ring buffer**) is a **fixed-size array** that wraps around when it reaches the end. Instead of shifting elements (like in an `ArrayList`), you just move two pointers:

* `front`: points to the first element
* `rear`: points to the position where the next element will be inserted

---

### 🧠 **Why is it Efficient for Queues?**

In a standard array-based queue:

* When you `dequeue`, you must shift all elements left → **O(n)** time.

In a circular buffer:

* Both `enqueue` and `dequeue` are done in **O(1)** time.
* No shifting needed — just update `front` and `rear` with modulo arithmetic.

---

### ✅ **Example Implementation Idea:**

```java
int[] queue = new int[5];
int front = 0, rear = 0, size = 0;

void enqueue(int val) {
    if (size == queue.length) return; // queue full
    queue[rear] = val;
    rear = (rear + 1) % queue.length;
    size++;
}

int dequeue() {
    if (size == 0) return -1; // queue empty
    int val = queue[front];
    front = (front + 1) % queue.length;
    size--;
    return val;
}
```

---

### 🔄 Real Usage

* Java’s `ArrayDeque` uses a **circular array** internally.
* `ArrayBlockingQueue` (in Java’s concurrent package) is a **thread-safe** circular buffer.

---

### 📌 Summary:

| Feature                 | Without Circular Buffer | With Circular Buffer |
| ----------------------- | ----------------------- | -------------------- |
| Dequeue time            | O(n) (due to shifting)  | O(1)                 |
| Space efficiency        | Poor if not managed     | Excellent            |
| Complexity to implement | Simple                  | Slightly more logic  |
