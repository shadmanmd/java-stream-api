# Java Stream API: Advanced Interview Practice (Answers)

Solutions for the 15 Java Stream API questions. Try each question yourself first, then compare.

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

**Solution:**

```java
Optional<Integer> secondHighest = numbers.stream()
        .distinct()                       // Stream<Integer>: 10, 20, 30, 40, 50
        .sorted(Comparator.reverseOrder()) // Stream<Integer>: 50, 40, 30, 20, 10
        .skip(1)                          // Stream<Integer>: 40, 30, 20, 10
        .findFirst();                     // Optional<Integer>: 40

secondHighest.ifPresent(System.out::println); // 40
```

**Explanation:** `distinct()` removes the duplicate 40 and 30, so the sorted order is clean. `skip(1)` drops the highest value, and `findFirst()` returns an `Optional`, which handles the case where the list is too short.

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

**Solution:**

```java
List<Integer> duplicates = numbers.stream()
        .collect(Collectors.groupingBy(n -> n, Collectors.counting())) // Map<Integer, Long>
        .entrySet().stream()                                           // Stream<Map.Entry<Integer, Long>>
        .filter(e -> e.getValue() > 1)                                 // keep counts above 1
        .map(Map.Entry::getKey)                                        // Stream<Integer>
        .collect(Collectors.toList());                                 // List<Integer>

System.out.println(duplicates); // [1, 2, 3]
```

**Explanation:** `groupingBy` with `counting()` builds a `Map<Integer, Long>` of frequencies. Streaming the entries lets you filter for counts greater than 1 and pull out the keys.

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

**Solution:**

```java
Map<Boolean, List<Integer>> partitioned = numbers.stream()
        .collect(Collectors.partitioningBy(n -> n % 2 == 0));

List<Integer> even = partitioned.get(true);  // [2, 4, 6, 8]
List<Integer> odd  = partitioned.get(false); // [1, 3, 5, 7]

System.out.println("Even: " + even);
System.out.println("Odd: " + odd);
```

**Explanation:** `partitioningBy` always returns a `Map<Boolean, List<T>>` with both keys present, even when one group is empty. That makes it safer than `groupingBy` for a true/false split.

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

**Solution:**

```java
List<Integer> top3 = numbers.stream()
        .distinct()
        .sorted(Comparator.reverseOrder())
        .limit(3)
        .collect(Collectors.toList());

System.out.println(top3); // [90, 80, 60]
```

**Explanation:** Remove duplicates first, otherwise 80 would appear twice in the top three. Then sort in descending order and take the first three.

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

**Solution:**

```java
Map<String, Long> frequency = words.stream()
        .collect(Collectors.groupingBy(w -> w, Collectors.counting()));

System.out.println(frequency); // {Java=3, Spring=2, React=1}
```

**Explanation:** `groupingBy` uses each word as the key, and `counting()` tallies occurrences. Note that `HashMap` does not guarantee order, so the printed order may differ from the expected output. Use `LinkedHashMap` via `groupingBy(w -> w, LinkedHashMap::new, Collectors.counting())` if insertion order matters.

---

## Level 2: Advanced (Custom Objects)

Consider the following `Employee` class for Q6 to Q10:

```java
class Employee {
    private int id;
    private String name;
    private String department;
    private double salary;

    public Employee(int id, String name, String department, double salary) {
        this.id = id;
        this.name = name;
        this.department = department;
        this.salary = salary;
    }

    public int getId() { return id; }
    public String getName() { return name; }
    public String getDepartment() { return department; }
    public double getSalary() { return salary; }
}
```

**Sample data used in Q6 to Q10:**

```java
List<Employee> employees = Arrays.asList(
    new Employee(1, "Rahul", "IT", 80000),
    new Employee(2, "Priya", "HR", 60000),
    new Employee(3, "Amit", "IT", 90000),
    new Employee(4, "Sneha", "Finance", 70000),
    new Employee(5, "Karan", "HR", 75000),
    new Employee(6, "Neha", "IT", 95000),
    new Employee(7, "Arjun", "Finance", 65000)
);
```

---

### Q6. Group employees by department.

**Expected output:**

```text
IT      -> Rahul, Amit, Neha
HR      -> Priya, Karan
Finance -> Sneha, Arjun
```

**Solution:**

```java
Map<String, List<Employee>> byDepartment = employees.stream()
        .collect(Collectors.groupingBy(Employee::getDepartment));

byDepartment.forEach((dept, list) -> {
    String names = list.stream()
            .map(Employee::getName)
            .collect(Collectors.joining(", "));
    System.out.println(dept + " -> " + names);
});
```

**Explanation:** `groupingBy` with a method reference produces a `Map<String, List<Employee>>`. The inner stream then extracts names and joins them into a string.

---

### Q7. Find the highest-paid employee in each department.

**Expected output:**

```text
IT      -> Neha (95000)
HR      -> Karan (75000)
Finance -> Sneha (70000)
```

**Solution:**

```java
Map<String, Optional<Employee>> topPaid = employees.stream()
        .collect(Collectors.groupingBy(
                Employee::getDepartment,
                Collectors.maxBy(Comparator.comparingDouble(Employee::getSalary))
        ));

topPaid.forEach((dept, opt) ->
        opt.ifPresent(e -> System.out.println(
                dept + " -> " + e.getName() + " (" + (int) e.getSalary() + ")")
        )
);
```

**Explanation:** `groupingBy` accepts a downstream collector. `maxBy` with `comparingDouble` picks the top employee in each group and wraps the result in `Optional`, since a group could theoretically be empty.

---

### Q8. Calculate the average salary of each department.

**Expected output:**

```text
IT      -> 88333.33
HR      -> 67500
Finance -> 67500
```

**Solution:**

```java
Map<String, Double> avgSalary = employees.stream()
        .collect(Collectors.groupingBy(
                Employee::getDepartment,
                Collectors.averagingDouble(Employee::getSalary)
        ));

avgSalary.forEach((dept, avg) -> System.out.println(dept + " -> " + avg));
```

**Explanation:** `averagingDouble` is a downstream collector that computes the mean of each group. IT is (80000 + 90000 + 95000) / 3 = 88333.33, while HR and Finance both come to 67500.

---

### Q9. Find the second-highest-paid employee across all departments.

**Expected output:**

```text
Amit -> 90000
```

**Solution:**

```java
Optional<Employee> secondHighest = employees.stream()
        .sorted(Comparator.comparingDouble(Employee::getSalary).reversed())
        .skip(1)
        .findFirst();

secondHighest.ifPresent(e ->
        System.out.println(e.getName() + " -> " + (int) e.getSalary())
);
```

**Explanation:** Sorting by salary in descending order puts Neha (95000) first and Amit (90000) second. `skip(1)` drops Neha, and `findFirst()` returns Amit.

---

### Q10. Find the department with the highest average salary.

**Expected output:**

```text
IT -> 88333.33
```

**Solution:**

```java
Optional<Map.Entry<String, Double>> topDept = employees.stream()
        .collect(Collectors.groupingBy(
                Employee::getDepartment,
                Collectors.averagingDouble(Employee::getSalary)
        ))
        .entrySet().stream()
        .max(Map.Entry.comparingByValue());

topDept.ifPresent(e ->
        System.out.println(e.getKey() + " -> " + e.getValue())
);
```

**Explanation:** First compute the average per department, then stream the entries and use `max` with `comparingByValue()` to find the highest average.

---

## Level 3: Challenging (Nested Collections and Maps)

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

**Solution:**

```java
List<Integer> flat = numbers.stream()
        .flatMap(List::stream)
        .collect(Collectors.toList());

System.out.println(flat); // [1, 2, 3, 4, 5, 6, 7, 8]
```

**Bonus (remove duplicates and sort):**

```java
List<Integer> flatSorted = numbers.stream()
        .flatMap(List::stream)
        .distinct()
        .sorted()
        .collect(Collectors.toList());
```

**Explanation:** `map(List::stream)` would give a `Stream<Stream<Integer>>`, which is not useful. `flatMap` takes each inner stream and merges them into a single `Stream<Integer>`.

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

**Solution:**

```java
employees.entrySet().stream()
        .filter(e -> e.getValue() > 70000)
        .forEach(e -> System.out.println(e.getKey() + "=" + e.getValue()));
```

**Bonus (collect into a new Map):**

```java
Map<String, Integer> highEarners = employees.entrySet().stream()
        .filter(e -> e.getValue() > 70000)
        .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue));
```

**Explanation:** Map streams work on `entrySet()`, since a `Map` itself is not a `Collection`. Sneha earns exactly 70000, which is not above 70000, so she is excluded.

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

**Solution:**

```java
List<Integer> common = list1.stream()
        .filter(list2::contains)
        .collect(Collectors.toList());

System.out.println(common); // [3, 4, 5]
```

**Bonus (using a Set for faster lookup):**

```java
Set<Integer> set2 = new HashSet<>(list2);

List<Integer> commonFast = list1.stream()
        .filter(set2::contains)
        .collect(Collectors.toList());
```

**Explanation:** `List.contains` is O(n), so the first solution is O(n × m) overall. Converting `list2` to a `HashSet` makes each lookup O(1), bringing the total to roughly O(n + m). In an interview, mentioning this trade-off earns points.

---

### Q14. Given a List of employees, create a Map where Key = Department, Value = List of employee names, including only employees earning more than ₹70,000.

**Input:** Use the Employee dataset from Q6.

**Expected output:**

```text
{
  IT=[Rahul, Amit, Neha],
  HR=[Karan],
  Finance=[]
}
```

**Solution:**

```java
Map<String, List<String>> result = employees.stream()
        .filter(e -> e.getSalary() > 70000)
        .collect(Collectors.groupingBy(
                Employee::getDepartment,
                Collectors.mapping(Employee::getName, Collectors.toList())
        ));

System.out.println(result);
// {IT=[Rahul, Amit, Neha], HR=[Karan]}
```

**Handling Finance as an empty list:**

Filtering before grouping removes Finance completely, because neither Sneha (70000) nor Arjun (65000) passes the filter. To keep Finance with an empty list, pre-populate the map with all departments:

```java
Map<String, List<String>> resultWithAll = new LinkedHashMap<>();
employees.stream().map(Employee::getDepartment).distinct()
        .forEach(dept -> resultWithAll.put(dept, new ArrayList<>()));

employees.stream()
        .filter(e -> e.getSalary() > 70000)
        .forEach(e -> resultWithAll.get(e.getDepartment()).add(e.getName()));

// {IT=[Rahul, Amit, Neha], HR=[Karan], Finance=[]}
```

**Explanation:** `mapping` is a downstream collector that transforms each grouped element before collecting it. This is more readable than building the list manually. The note in the PDF is correct: `filter` before `groupingBy` drops departments with no qualifying employees.

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

**Solution:**

```java
Optional<Map.Entry<Integer, Long>> mostFrequent = numbers.stream()
        .collect(Collectors.groupingBy(n -> n, Collectors.counting()))
        .entrySet().stream()
        .max(Map.Entry.comparingByValue());

mostFrequent.ifPresent(e ->
        System.out.println(e.getKey() + " -> " + e.getValue() + " occurrences")
);
// 3 -> 4 occurrences
```

**Explanation:** Count each value with `groupingBy` and `counting()`, then find the entry with the largest count using `max`. In a tie, `max` returns one of the tied entries, so mention that to the interviewer if they ask about ties.

---

## Quick Reference

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
