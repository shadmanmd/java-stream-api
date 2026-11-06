# Java Stream API: Advanced Interview Practice

A practice set of 15 Java Stream API questions, ordered by difficulty.

**Practice roadmap**

| Level | Difficulty | Focus |
|-------|-----------|-------|
| 1 | Intermediate | Filtering, mapping, sorting and collecting (Strings, Lists) |
| 2 | Advanced | Custom objects and POJOs |
| 3 | Challenging | Nested collections and Maps |

**Interview tip:** For every question, explain the return type after each intermediate operation (`map`, `filter`, `collect`, and so on). That helps with follow-up questions in a technical interview.

---

## Level 1: Intermediate

These test your understanding of filtering, mapping, sorting and collecting.

### Q1. Find the second-highest distinct number in a List.

**Input:**

```java
List<Integer> numbers = Arrays.asList(10, 20, 30, 40, 40, 30, 50);
```

**Expected output:**

```text
40
```

**Concepts:** `distinct()`, `sorted()`, `skip()`, `findFirst()`

---

### Q2. Find all elements that appear more than once in a List.

**Input:**

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 2, 4, 5, 1, 6, 3);
```

**Expected output:**

```text
[1, 2, 3]
```

**Concepts:** `groupingBy()`, `counting()`, `filter()`

---

### Q3. Separate even and odd numbers into two Lists.

**Input:**

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8);
```

**Expected result:**

```text
Even: [2, 4, 6, 8]
Odd: [1, 3, 5, 7]
```

**Concepts:** `partitioningBy()`

---

### Q4. Find the top three highest numbers without duplicates.

**Input:**

```java
List<Integer> numbers = Arrays.asList(10, 50, 20, 80, 80, 60, 90);
```

**Expected output:**

```text
[90, 80, 60]
```

**Concepts:** `distinct()`, `sorted()`, `limit()`

---

### Q5. Find the frequency of every word in a List.

**Input:**

```java
List<String> words = Arrays.asList(
    "Java", "Spring", "Java",
    "React", "Spring", "Java"
);
```

**Expected output:**

```text
{
  Java=3,
  Spring=2,
  React=1
}
```

**Concepts:** `groupingBy()`, `counting()`

---

## Level 2: Advanced (Custom Objects)

Now things become more interesting. These are particularly useful for backend development interviews.

Consider the following `Employee` class for Q6 to Q10:

```java
class Employee {
    private int id;
    private String name;
    private String department;
    private double salary;

    // Constructor, getters and setters
}
```

**Assume the following employee records:**

| ID | Name | Department | Salary |
|----|------|------------|--------|
| 1 | Rahul | IT | 80000 |
| 2 | Priya | HR | 60000 |
| 3 | Amit | IT | 90000 |
| 4 | Sneha | Finance | 70000 |
| 5 | Karan | HR | 75000 |
| 6 | Neha | IT | 95000 |
| 7 | Arjun | Finance | 65000 |

---

### Q6. Group employees by department.

**Expected output:**

```text
IT      -> Rahul, Amit, Neha
HR      -> Priya, Karan
Finance -> Sneha, Arjun
```

**Concepts:** `groupingBy()`

---

### Q7. Find the highest-paid employee in each department.

**Expected output:**

```text
IT      -> Neha (95000)
HR      -> Karan (75000)
Finance -> Sneha (70000)
```

**Concepts:** `groupingBy()`, `maxBy()`, `Comparator.comparingDouble()`

---

### Q8. Calculate the average salary of each department.

**Expected output:**

```text
IT      -> 88333.33
HR      -> 67500
Finance -> 67500
```

**Concepts:** `groupingBy()`, `averagingDouble()`

---

### Q9. Find the second-highest-paid employee across all departments.

**Expected output:**

```text
Amit -> 90000
```

**Concepts:** `sorted()`, `Comparator`, `skip()`, `findFirst()`

---

### Q10. Find the department with the highest average salary.

**Expected output:**

```text
IT -> 88333.33
```

**Concepts:** `groupingBy()`, `averagingDouble()`, `entrySet().stream()`, `max()`

---

## Level 3: Challenging (Nested Collections and Maps)

These are the types of questions that test whether you genuinely understand Streams rather than just memorising methods.

### Q11. Flatten a List of Lists into a single List.

**Input:**

```java
List<List<Integer>> numbers = Arrays.asList(
    Arrays.asList(1, 2, 3),
    Arrays.asList(4, 5),
    Arrays.asList(6, 7, 8)
);
```

**Expected output:**

```text
[1, 2, 3, 4, 5, 6, 7, 8]
```

**Concepts:** `flatMap()`

**Bonus:** Remove duplicates and sort the final result.

---

### Q12. Given a Map of employees and their salaries, find employees earning above 70,000.

**Input:**

```java
Map<String, Integer> employees = new HashMap<>();

employees.put("Rahul", 80000);
employees.put("Priya", 60000);
employees.put("Amit", 90000);
employees.put("Sneha", 70000);
employees.put("Karan", 75000);
```

**Expected output:**

```text
Rahul=80000
Amit=90000
Karan=75000
```

**Concepts:** `entrySet().stream()`, `filter()`, `collect()`

**Bonus:** Collect the result into a new Map instead of printing it.

---

### Q13. Find common elements between two Lists.

**Input:**

```java
List<Integer> list1 = Arrays.asList(1, 2, 3, 4, 5);
List<Integer> list2 = Arrays.asList(3, 4, 5, 6, 7);
```

**Expected output:**

```text
[3, 4, 5]
```

**Concepts:** `filter()`, `contains()`, `collect()`

**Bonus:** Solve it using a `Set` to improve lookup efficiency.

---

### Q14. Given a List of employees, create a Map where:

- Key = Department
- Value = List of employee names
- Only employees earning more than ₹70,000 should be included.

**Input:** Use the Employee dataset from Q6.

**Expected output:**

```text
{
  IT=[Rahul, Amit, Neha],
  HR=[Karan],
  Finance=[]
}
```

**Concepts:** `filter()`, `groupingBy()`, `mapping()`, `Collectors.toList()`

**Note:** If you use `filter()` before grouping, departments with no qualifying employees won't appear. If you want Finance as an empty list, you'll need to account for that separately.

---

### Q15. Find the most frequently occurring element in a List.

**Input:**

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 2, 4, 2, 5, 3, 3, 3);
```

**Expected output:**

```text
3 -> 4 occurrences
```

**Concepts:** `groupingBy()`, `counting()`, `entrySet()`, `max()`

---

## Important Stream API Methods to Master

| Method | Purpose |
|--------|---------|
| `map()` | Transform each element |
| `flatMap()` | Flatten nested collections |
| `filter()` | Filter elements |
| `distinct()` | Remove duplicates |
| `sorted()` | Sort elements |
| `limit()` | Limit results |
| `skip()` | Skip elements |
| `reduce()` | Aggregate into one result |
| `collect()` | Collect into a collection or result |
| `groupingBy()` | Group elements |
| `partitioningBy()` | Divide into two groups |
| `mapping()` | Transform downstream elements |
| `counting()` | Count elements |
| `maxBy()` / `minBy()` | Find maximum or minimum |
| `averagingDouble()` | Calculate average |
| `summarizingInt()` | Get count, sum, min, max and average |

---

## Practice Tips

1. **Don't memorise the final code.** Explain the return type after each intermediate operation.
2. **Solve one question at a time** before moving to the next level.
3. **Time yourself** on Level 3 questions. They reflect real interview pressure.
