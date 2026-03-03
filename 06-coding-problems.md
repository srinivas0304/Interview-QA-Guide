# Java Coding Problems & Solutions

## Table of Contents
1. [String Problems](#string-problems)
2. [Array Problems](#array-problems)
3. [Collection Problems](#collection-problems)
4. [Logic Problems](#logic-problems)
5. [Interview-Level Problems](#interview-level-problems)

---

## String Problems

### Problem 1: Reverse a String

**Question:** Write a program to reverse a string without using built-in `reverse()` method.

**Solution 1: Using Loop**
```java
public class StringReversal {
    
    public static String reverseString(String str) {
        String reversed = "";
        for (int i = str.length() - 1; i >= 0; i--) {
            reversed += str.charAt(i);
        }
        return reversed;
    }

    public static void main(String[] args) {
        String str = "Hello World";
        System.out.println("Original: " + str);
        System.out.println("Reversed: " + reverseString(str)); // dlroW olleH
    }
}
```

**Solution 2: Using StringBuilder (Better)**
```java
public static String reverseString(String str) {
    return new StringBuilder(str).reverse().toString();
}
```

**Solution 3: Using Recursion**
```java
public static String reverseString(String str) {
    if (str.isEmpty()) {
        return str;
    }
    return reverseString(str.substring(1)) + str.charAt(0);
}
```

---

### Problem 2: Check if String is Palindrome

**Question:** Write a program to check if a string is palindrome (reads same forwards and backwards).

**Example:** "racecar" → true, "hello" → false

**Solution:**
```java
public class PalindromeCheck {
    
    public static boolean isPalindrome(String str) {
        // Remove spaces and convert to lowercase
        String clean = str.replaceAll(" ", "").toLowerCase();
        
        // Compare with reverse
        String reverse = new StringBuilder(clean).reverse().toString();
        
        return clean.equals(reverse);
    }

    public static void main(String[] args) {
        System.out.println(isPalindrome("race car"));      // true
        System.out.println(isPalindrome("hello"));          // false
        System.out.println(isPalindrome("A man a plan a canal Panama")); // true
    }
}
```

**Better Solution with Pointer Approach:**
```java
public static boolean isPalindrome(String str) {
    str = str.replaceAll(" ", "").toLowerCase();
    
    int left = 0;
    int right = str.length() - 1;
    
    while (left < right) {
        if (str.charAt(left) != str.charAt(right)) {
            return false;
        }
        left++;
        right--;
    }
    return true;
}
```

---

### Problem 3: Count Character Frequency

**Question:** Write a program to count the frequency of each character in a string.

**Example:** "aabbcc" → {a: 2, b: 2, c: 2}

**Solution 1: Using HashMap**
```java
public class CharacterFrequency {
    
    public static Map<Character, Integer> countFrequency(String str) {
        Map<Character, Integer> frequency = new HashMap<>();
        
        for (char c : str.toCharArray()) {
            frequency.put(c, frequency.getOrDefault(c, 0) + 1);
        }
        
        return frequency;
    }

    public static void main(String[] args) {
        String str = "aabbcc";
        Map<Character, Integer> freq = countFrequency(str);
        
        for (Map.Entry<Character, Integer> entry : freq.entrySet()) {
            System.out.println(entry.getKey() + " : " + entry.getValue());
        }
        // Output:
        // a : 2
        // b : 2
        // c : 2
    }
}
```

**Solution 2: Using Java 8 Streams**
```java
public static Map<Character, Long> countFrequency(String str) {
    return str.chars()
        .mapToObj(c -> (char) c)
        .collect(Collectors.groupingBy(Function.identity(), 
            Collectors.counting()));
}
```

---

### Problem 4: Remove Duplicates from String

**Question:** Write a program to remove duplicate characters from a string.

**Example:** "aabbcc" → "abc"

**Solution 1: Using HashSet**
```java
public class RemoveDuplicates {
    
    public static String removeDuplicates(String str) {
        Set<Character> chars = new LinkedHashSet<>();
        
        for (char c : str.toCharArray()) {
            chars.add(c);
        }
        
        StringBuilder sb = new StringBuilder();
        for (char c : chars) {
            sb.append(c);
        }
        
        return sb.toString();
    }

    public static void main(String[] args) {
        System.out.println(removeDuplicates("aabbcc")); // abc
        System.out.println(removeDuplicates("hello"));   // helo
    }
}
```

**Solution 2: Using Java 8 Streams**
```java
public static String removeDuplicates(String str) {
    return str.chars()
        .distinct()
        .collect(StringBuilder::new, 
            (sb, c) -> sb.append((char) c),
            StringBuilder::append)
        .toString();
}
```

---

## Array Problems

### Problem 5: Find Second Largest Number in Array

**Question:** Write a program to find the second largest number in an array.

**Example:** [10, 5, 8, 20, 15] → 15

**Solution 1: Using Sorting**
```java
public class SecondLargest {
    
    public static int findSecondLargest(int[] arr) {
        if (arr.length < 2) {
            return -1; // Invalid input
        }
        
        Arrays.sort(arr);
        return arr[arr.length - 2];
    }

    public static void main(String[] args) {
        int[] arr = {10, 5, 8, 20, 15};
        System.out.println(findSecondLargest(arr)); // 15
    }
}
```

**Solution 2: Single Pass (Optimal)**
```java
public static int findSecondLargest(int[] arr) {
    if (arr.length < 2) {
        return -1;
    }
    
    int largest = Integer.MIN_VALUE;
    int secondLargest = Integer.MIN_VALUE;
    
    for (int num : arr) {
        if (num > largest) {
            secondLargest = largest;
            largest = num;
        } else if (num > secondLargest && num < largest) {
            secondLargest = num;
        }
    }
    
    return secondLargest;
}
```

---

### Problem 6: Find Missing Number in Array

**Question:** Given an array containing numbers 1 to n (one number missing), find the missing number.

**Example:** [1, 2, 3, 5, 6, 7] (n=7) → 4

**Solution 1: Using Sum Formula**
```java
public class MissingNumber {
    
    public static int findMissing(int[] arr) {
        int n = arr.length + 1; // Total numbers should be n+1
        
        // Sum of 1 to n
        int expectedSum = n * (n + 1) / 2;
        
        // Sum of array elements
        int actualSum = 0;
        for (int num : arr) {
            actualSum += num;
        }
        
        return expectedSum - actualSum;
    }

    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 5, 6, 7};
        System.out.println(findMissing(arr)); // 4
    }
}
```

**Solution 2: Using XOR**
```java
public static int findMissing(int[] arr) {
    int xor = 0;
    
    // XOR all array elements
    for (int num : arr) {
        xor ^= num;
    }
    
    // XOR with 1 to n
    int n = arr.length + 1;
    for (int i = 1; i <= n; i++) {
        xor ^= i;
    }
    
    return xor;
}
```

---

### Problem 7: Find Common Elements in Two Arrays

**Question:** Write a program to find common elements in two arrays.

**Example:** [1, 2, 3, 4] and [3, 4, 5, 6] → [3, 4]

**Solution:**
```java
public class CommonElements {
    
    public static List<Integer> findCommon(int[] arr1, int[] arr2) {
        Set<Integer> set1 = new HashSet<>(Arrays.stream(arr1).boxed()
            .collect(Collectors.toList()));
        Set<Integer> set2 = new HashSet<>(Arrays.stream(arr2).boxed()
            .collect(Collectors.toList()));
        
        set1.retainAll(set2); // Keep only common elements
        
        return new ArrayList<>(set1);
    }

    public static void main(String[] args) {
        int[] arr1 = {1, 2, 3, 4};
        int[] arr2 = {3, 4, 5, 6};
        System.out.println(findCommon(arr1, arr2)); // [3, 4]
    }
}
```

---

### Problem 8: Rotate Array

**Question:** Write a program to rotate an array by k positions.

**Example:** [1, 2, 3, 4, 5], k=2 → [4, 5, 1, 2, 3]

**Solution:**
```java
public class RotateArray {
    
    public static void rotateArray(int[] arr, int k) {
        k = k % arr.length; // Handle k > arr.length
        
        reverse(arr, 0, arr.length - 1);
        reverse(arr, 0, k - 1);
        reverse(arr, k, arr.length - 1);
    }
    
    private static void reverse(int[] arr, int start, int end) {
        while (start < end) {
            int temp = arr[start];
            arr[start] = arr[end];
            arr[end] = temp;
            start++;
            end--;
        }
    }

    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5};
        rotateArray(arr, 2);
        System.out.println(Arrays.toString(arr)); // [4, 5, 1, 2, 3]
    }
}
```

---

## Collection Problems

### Problem 9: Remove Duplicates from ArrayList

**Question:** Write a program to remove duplicate elements from an ArrayList.

**Solution 1: Using HashSet**
```java
public class RemoveDuplicatesFromList {
    
    public static List<Integer> removeDuplicates(List<Integer> list) {
        return new ArrayList<>(new HashSet<>(list));
    }

    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(1, 2, 2, 3, 3, 4, 5, 5);
        System.out.println(removeDuplicates(list)); // [1, 2, 3, 4, 5]
    }
}
```

**Solution 2: Using Java 8 Streams**
```java
public static List<Integer> removeDuplicates(List<Integer> list) {
    return list.stream()
        .distinct()
        .collect(Collectors.toList());
}
```

---

### Problem 10: Sort HashMap by Values

**Question:** Write a program to sort a HashMap by values.

**Example:** {a: 3, b: 1, c: 2} → {b: 1, c: 2, a: 3}

**Solution:**
```java
public class SortHashMapByValue {
    
    public static Map<String, Integer> sortByValue(Map<String, Integer> map) {
        return map.entrySet()
            .stream()
            .sorted(Map.Entry.comparingByValue())
            .collect(Collectors.toLinkedHashMap(
                Map.Entry::getKey,
                Map.Entry::getValue
            ));
    }

    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("a", 3);
        map.put("b", 1);
        map.put("c", 2);
        
        Map<String, Integer> sorted = sortByValue(map);
        System.out.println(sorted); // {b=1, c=2, a=3}
    }
}
```

---

## Logic Problems

### Problem 11: Fibonacci Series

**Question:** Write a program to print Fibonacci series up to n numbers.

**Example:** n=5 → 0, 1, 1, 2, 3

**Solution 1: Iterative**
```java
public class FibonacciSeries {
    
    public static void printFibonacci(int n) {
        int a = 0, b = 1;
        
        System.out.print(a + " ");
        
        for (int i = 1; i < n; i++) {
            System.out.print(b + " ");
            int temp = a + b;
            a = b;
            b = temp;
        }
    }

    public static void main(String[] args) {
        printFibonacci(5); // 0 1 1 2 3
    }
}
```

**Solution 2: Recursive**
```java
public static int fibonacci(int n) {
    if (n <= 1) {
        return n;
    }
    return fibonacci(n - 1) + fibonacci(n - 2);
}

public static void main(String[] args) {
    for (int i = 0; i < 5; i++) {
        System.out.print(fibonacci(i) + " "); // 0 1 1 2 3
    }
}
```

---

### Problem 12: Check Prime Number

**Question:** Write a program to check if a number is prime.

**Solution:**
```java
public class PrimeNumber {
    
    public static boolean isPrime(int num) {
        if (num <= 1) {
            return false;
        }
        if (num == 2) {
            return true;
        }
        if (num % 2 == 0) {
            return false;
        }
        
        for (int i = 3; i <= Math.sqrt(num); i += 2) {
            if (num % i == 0) {
                return false;
            }
        }
        
        return true;
    }

    public static void main(String[] args) {
        System.out.println(isPrime(17));  // true
        System.out.println(isPrime(10));  // false
    }
}
```

---

## Interview-Level Problems

### Problem 13: Two Sum Problem

**Question:** Given an array and a target sum, find indices of two numbers that add up to target.

**Example:** [2, 7, 11, 15], target=9 → [0, 1]

**Solution:**
```java
public class TwoSum {
    
    public static int[] findTwoSum(int[] arr, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        
        for (int i = 0; i < arr.length; i++) {
            int complement = target - arr[i];
            
            if (map.containsKey(complement)) {
                return new int[]{map.get(complement), i};
            }
            
            map.put(arr[i], i);
        }
        
        return new int[]{}; // No solution found
    }

    public static void main(String[] args) {
        int[] arr = {2, 7, 11, 15};
        int[] result = findTwoSum(arr, 9);
        System.out.println(Arrays.toString(result)); // [0, 1]
    }
}
```

---

### Problem 14: Three Sum Problem (Find Triplets)

**Question:** Find all unique triplets in array that sum to a target.

**Example:** [-1, 0, 1, 2, -1, -4], target=0 → [[-1, -1, 2], [-1, 0, 1]]

**Solution:**
```java
public class ThreeSum {
    
    public static List<List<Integer>> findTriplets(int[] arr, int target) {
        Arrays.sort(arr);
        List<List<Integer>> result = new ArrayList<>();
        
        for (int i = 0; i < arr.length - 2; i++) {
            // Avoid duplicates
            if (i > 0 && arr[i] == arr[i - 1]) {
                continue;
            }
            
            int left = i + 1;
            int right = arr.length - 1;
            
            while (left < right) {
                int sum = arr[i] + arr[left] + arr[right];
                
                if (sum == target) {
                    result.add(Arrays.asList(arr[i], arr[left], arr[right]));
                    
                    // Skip duplicates
                    while (left < right && arr[left] == arr[left + 1]) {
                        left++;
                    }
                    while (left < right && arr[right] == arr[right - 1]) {
                        right--;
                    }
                    
                    left++;
                    right--;
                } else if (sum < target) {
                    left++;
                } else {
                    right--;
                }
            }
        }
        
        return result;
    }

    public static void main(String[] args) {
        int[] arr = {-1, 0, 1, 2, -1, -4};
        System.out.println(findTriplets(arr, 0)); 
        // [[-1, -1, 2], [-1, 0, 1]]
    }
}
```

---

### Problem 15: Longest Substring Without Repeating Characters

**Question:** Find length of