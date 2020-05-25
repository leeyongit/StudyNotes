# PHP 使用技巧

## 1. PHP中的数组到底占用多大的空间？
[PHP数组到底占用多少内存空间](http://www.ituring.com.cn/article/498349)
构造一个含有100000个不重复的整型元素的数组并且测量其占用的内存数量，实现代码如下：
```php
// 记录开始内存状态
$startMemory = memory_get_usage();
// 生成包含1-100000为数据元素的数组
$array = range(1, 100000);
// 获取数组占用的内存空间大小（单位字节:bytes）
echo memory_get_usage() - $startMemory, ' bytes';
```

## 2. php利用array_search与array_column实现二维数组查找
利用array_search与array_column实现二维数组查找，不用自己写个循环，减少工作量。
```php
$userdb = array(
    0 => array(
            'uid' => 100,
            'name' => 'Sandra Shush',
            'url' => 'urlof100'
        ),

    1 => array(
            'uid' => 5465,
            'name' => 'Stefanie Mcmohn',
            'pic_square' => 'urlof100'
        ),

    2 => Array(
            'uid' => 40489,
            'name' => 'Michael',
            'pic_square' => 'urlof40489'
        )
);

$found_key = array_search(40489, array_column($userdb, 'uid'));
/**
    如果$userdb很大，建议使用一个变量，避免搜索每个元素时都调用array_column()
    $uid = array_column($userdb, 'uid');
    $found_key = array_search(40489, $uid);
 */
var_dump($found_key);
```

## 3. PHP高效导出Excel(CSV)
CSV，是Comma Separated Value（逗号分隔值）的英文缩写，通常都是纯文本文件。
如果你导出的Excel没有什么高级用法的话，只是做导出数据用那么建议使用本方法,要比PHPexcel要高效的多。
二十万数据导出大概需要2到3秒。
```php
 /**
 * 导出excel(csv)
 * @data 导出数据
 * @headlist 第一行,列名
 * @fileName 输出Excel文件名
 */
function csv_export($data = array(), $headlist = array(), $fileName) {

    header('Content-Type: application/vnd.ms-excel');
    header('Content-Disposition: attachment;filename="'.$fileName.'.csv"');
    header('Cache-Control: max-age=0');

    //打开PHP文件句柄,php://output 表示直接输出到浏览器
    $fp = fopen('php://output', 'a');

    //输出Excel列名信息
    foreach ($headlist as $key => $value) {
        //CSV的Excel支持GBK编码，一定要转换，否则乱码
        $headlist[$key] = iconv('utf-8', 'gbk', $value);
    }

    //将数据通过fputcsv写到文件句柄
    fputcsv($fp, $headlist);

    //计数器
    $num = 0;

    //每隔$limit行，刷新一下输出buffer，不要太大，也不要太小
    $limit = 100000;

    //逐行取出数据，不浪费内存
    $count = count($data);
    for ($i = 0; $i < $count; $i++) {

        $num++;

        //刷新一下输出buffer，防止由于数据过多造成问题
        if ($limit == $num) {
            ob_flush();
            flush();
            $num = 0;
        }

        $row = $data[$i];
        foreach ($row as $key => $value) {
            $row[$key] = iconv('utf-8', 'gbk', $value);
        }

        fputcsv($fp, $row);
    }
  }
```