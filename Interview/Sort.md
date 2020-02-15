
### 1. 快速排序
快速排序使用分治法（Divide and conquer）策略来把一个串行（list）分为两个子串行（sub-lists）。

快速排序又是一种分而治之思想在排序算法上的典型应用。本质上来看，快速排序应该算是在冒泡排序基础上的递归分治法。
```php
$arr = [4, 5, 3, 2];
function quickSort($arr) {
    if (count($arr) <= 1)
        return $arr;
    $middle = $arr['0'];
    $leftArray = array();
    $rightArray = array();

    for ($i = 1; $i < count($arr); $i++) {
        if ($arr[$i] > $middle)
            $rightArray[] = $arr[$i]; // 5
        else
            $leftArray[] = $arr[$i]; // 3, 2
    }
    $leftArray = quickSort($leftArray);
    $leftArray[] = $middle;
    $rightArray = quickSort($rightArray);
    return array_merge($leftArray, $rightArray);
}

```

### 2. 冒泡排序
```php
function bubbleSort($arr) {
    $len = count($arr);
    for ($i=0; $i < $len - 1; $i++) {
        for ($j=0; $i < $len - 1 - $i; $j++) {
            if ($arr[$j] > $arr[$j+1]) {
                $tmp = $arr[$j];
                $arr[$j] = $arr[$j+1];
                $arr[$j+1] = $tmp;
            }
        }
    }
    return $arr;
}
```