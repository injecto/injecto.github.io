В процессе решения задачек на LeetCode натолкнулся на [забавный пазл](https://leetcode.com/problems/longest-repeating-character-replacement/), 
который заставил хорошо поскрипеть мозгом. Я бы вряд ли решил его без подглядывания в problem discussion, но и предложенные там
объяснения выглядят не идеально. Что ж, добавлю еще одно неидеальное объяснение.

Итак, сама проблема:

> You are given a string s and an integer k. You can choose any character of the string and change it to any other uppercase English character. You can perform this operation at most k times. Return the length of the longest substring containing the same letter you can get after performing the above operations.

> Example:
> Input: s = "AABABBA", k = 1
> Solution: Replace the one 'A' in the middle with 'B' and form "AABBBBA". The substring "BBBB" has the longest repeating letters, which is 4.

Предлагаю подумать перед тем, как прочитать решение.

<details>
    <summary>Подсказка 1</summary>
    <p>Задача может быть решена за линейное время.</p>
</details>

<details>
    <summary>Подсказка 2</summary>
    <p>Подходящая подстрока может быть выражена скользящим окном. Нужно хорошо сформулировать его инвариант.</p>
</details>

Итак, одно из возможных решений: подходящая подстрока может быть выражена скользящим окном, у которого
правая граница всегда движется вправо, а левая корректируется в случае, если окно нарушает инвариант:

```
windowLength - mostFrequentCharInWindowNumber <= k
```

То есть мы разрешаем внутри окна иметь не более k символов, отличных от наиболее частого. Соответственно, итоговый результат -- наибольший размер окна.

Пример реализации на Java:

```java
class Solution {
    public int characterReplacement(String s, int k) {
        var charCount = new int[26]; // sliding window's characters, at most 26 uppercase english letters
        var l = 0; // left index of window
        var res = 0; // result
        for (int rI = 0; rI < s.length(); rI++) { // right index of window, including!
            charCount[s.charAt(rI)-'A']++; // `someChar - 'A'` will take an 0-bases index in `charCount`
            while ((rI+1-l) - max(charCount) > k) { // while invariant is violated, move the left border
                charCount[s.charAt(l)-'A']--;
                l++;
            }
            res = Math.max(res, rI+1-l);
        }
        return res;
    }
    
    // Auxilary function returning any of largests element.
    int max(int[] a) {
        var res = a[0];
        for (int i = 0; i < a.length; i++) {
            if (res < a[i]) res = a[i];
        }
        return res;
    }
}
```
