# KMP - 字符串匹配算法

## 代码实现

```java
**
 * 字符串工具类，KMP匹配
 */
public class KmpUtils {

    /**
     * 计算next函数
     *
     * @param pat
     * @return
     */
    private static int[] next(String pat) {
        if (pat.length() == 0)
            return null;
        char[] pattern = pat.toCharArray();
        int len = pattern.length;
        int[] next = new int[len];
        next[0] = -1;
        for (int i = 1; i < len; i++) {
            int index = next[i - 1];
            int j = next[i - 1] + 1;
            if (pattern[i] == pattern[j]) {
                next[i] = ++index;
            } else {
                j = 0;  //从第一个位置比较
                index = -1;
                if (pattern[i] == pattern[j]) {
                    next[i] = ++index;
                } else {
                    next[i] = -1;
                }
            }
        }
        return next;
    }

    /**
     * 计算匹配的位置
     * @param string
     * @param pattern
     * @return
     */
    public static int match(String string, String pattern) {
        int i = 0, j = 0;
        char[] str = string.toCharArray();
        char[] pat = pattern.toCharArray();
        int lens = str.length;
        int lenp = pat.length;
        int[] next = next(pattern);
        while (i < lens && j < lenp) {
            if (str[i] == pat[j]) {
                i++;
                j++;
            } else if (j == 0) {
                i++;
            } else {
                j = next[j - 1] + 1;
            }
        }
        return ((j == lenp) ? (i - lenp) : -1);
    }

    public static void main(String[] args) {
        System.out.println(match("abaababce", "ababc"));
    }
}
```
