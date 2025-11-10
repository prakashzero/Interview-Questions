# README — Java Strings & Memory Management (Easy Guide)

## ✅ 1. What is a String?

A **String** in Java represents a sequence of characters.
Example:

```java
String name = "Bhanu";
```

* Strings are **immutable** → cannot be changed after creation.
* Any modification creates a **new object**.

---

## ✅ 2. Why are Strings Immutable?

Reasons:

* **Security** (e.g., passwords)
* **Caching** (string pool usage)
* **Thread-safety**
* **Class loading consistency**

Example:

```java
String s = "Bhanu";
s = s + "prak"; // Creates NEW string "Bhanuprak"
```

---

## ✅ 3. String Constant Pool (SCP)

* A **special memory area inside the Heap**
* Stores **unique** String literals
* Maintains one copy of each literal

Example:

```java
String a = "Hello";
String b = "Hello"; // reused from pool
```

Both references point to the same pooled object.

---

## ✅ 4. Heap vs String Pool

Think of the Heap as a large memory box:

```
Heap
 ├── String Pool (for literals & interned strings)
 └── Regular Heap (for objects created using new)
```

* **Heap:** stores objects
* **String Pool:** stores unique literals & interned strings

---

## ✅ 5. Stack vs Heap

| Stack                                     | Heap                         |
| ----------------------------------------- | ---------------------------- |
| Stores reference variables, method frames | Stores objects               |
| Fast access                               | Slower                       |
| Per-thread                                | Shared across threads        |
| Auto-managed                              | Managed by Garbage Collector |

**Reference is in Stack → Value/object is in Heap**

---

## ✅ 6. Using `new String("text")`

`new` **forces** creation of a new object in regular Heap, even if the same literal already exists in pool.

Example:

```java
String x = new String("Bhanu");
```

Memory created:

* `"Bhanu"` in pool (if not present)
* `"Bhanu"` in heap (new object)

Total objects = **2**

---

## ✅ 7. == vs equals()

### `==`

* Compares memory **references**
* Checks if both variables point to the **same object**

### `equals()`

* Compares **values/content**

Example:

```java
String s1 = "Bhanu";
String s2 = "Bhanu";
String s3 = new String("Bhanu");

s1 == s2       // true (same pool object)
s1 == s3       // false (heap vs pool)
s1.equals(s3)  // true (text is same)
```

---

## ✅ 8. intern()

### What it does:

* Returns a **canonical** (shared) string from the String Pool.
intern() ensures that a string has exactly one shared copy in the String Pool. If the pool doesn’t already contain the string, the JVM adds it there.

### Two cases:

#### ✅ Case 1: String exists in pool

```java
String s = new String("Bhanu");
String p = s.intern();
```

* Pool already has `"Bhanu"`
* `intern()` returns reference to pool string
* It does **not** move heap object to pool

#### ✅ Case 2: String not in pool

```java
String s = new String("NewString123");
String p = s.intern();
```

* Pool does NOT have `"NewString123"`
* JVM adds it to pool
* Returns pooled reference

---

## ✅ 9. Important intern() clarification

❌ Interning does NOT “move” the heap object into the pool.
✅ It creates/fetches a pooled version.

---

## ✅ 10. StringBuilder vs StringBuffer vs String

| Type          | Mutable? | Thread-safe? | Fast?                        |
| ------------- | -------- | ------------ | ---------------------------- |
| String        | ❌ No     | ✅ Yes        | ❌ Slow for repeated ops      |
| StringBuilder | ✅ Yes    | ❌ No         | ✅ Fast                       |
| StringBuffer  | ✅ Yes    | ✅ Yes        | ⚠️ Slower than StringBuilder |

Use **StringBuilder** for most cases.

Example:

```java
StringBuilder sb = new StringBuilder();
sb.append("Bhanu");
sb.append("prak");
```

---

## ✅ 11. Common String Operations

```java
length()
charAt()
indexOf()
substring()
toUpperCase()
toLowerCase()
replace()
equalsIgnoreCase()
split()
trim()
contains()
startsWith()
endsWith()
```

---

## ✅ 12. Memory Overview (Final Summary)

* Literal → Stored in **String Pool**
* `new String()` → Stored in **Heap**
* Reference variables → Stored in **Stack**
* `intern()` → Returns pooled reference (creates one if missing)
* String is **immutable**

---

## ✅ 13. Visual Diagram Summary

```
                Stack
          ------------------
          s1 --> reference
          s2 --> reference
          s3 --> reference

                |
                v

                Heap
         -----------------------
         |   String Pool       |
         |  "Bhanu" <-- s1,s2  |
         |----------------------|
         |  Regular Heap        |
         |  new String("Bhanu") |
         |        ^             |
         |        |             |
         |       s3             |
         ------------------------
```

---

## ✅ 14. Interview One-Liners

* **String is immutable** → any change creates a new object.
* **String Pool** stores unique literals inside Heap.
* **new String()** always creates a new object in Heap.
* **Stack stores references** → Heap stores objects.
* **intern()** ensures only one shared copy exists in pool.
* **== compares references**, `equals()` compares values.
