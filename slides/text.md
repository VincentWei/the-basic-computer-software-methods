## 文字：字符集及编码

- 对文字进行编码的需求
- 各种字符集及编码的混战
- Unicode 的胜利
- Unicode 字符集及其编码

		
## 1. 对文字进行编码的需求

听个故事

		
## 2. ASCII 字符集的特点

* < 0x80
* 分类：
   - 控制字符（ < 0x20）
   - 制表符
   - 换行符
   - 空白字符（whitespace）
   - 大写字母
   - 小写字母
   - 标点符号

		
## 3. 问题

为什么 ASCII 只使用了七位二进制数据？

		
## 4. 如何表示英语之外的文字？

* ASCII 只考虑到了英语
* 其他语言怎么办？

		
## 5. 各种字符集及编码的混战

* EUC：Unix 扩展编码（IBM）
* ISO8859 系列字符集
* GB2312 -> GBK -> GB18030
* BIG5
* ISO10646

		
## 6. 字符集和编码的区别

		
## 7. Unicode 的历史

* Unicode 最早由微软、苹果、IBM 等发起。
* 1991 年 Unicode 变身财团，发布了 Unicode 1.0。
* 1997 年发布 Unicode 2.0。
* Unicode 3.0 总共定义了 65,534 个码位（0xFFFE 和 0xFFFF 为非字符）。
* 目前最新版本为 Unicode 14.0.0（2021 年 9 月发布）。

		
## 8. Unicode 的特点和术语

* Unicode 有 0x10FFFF 个码点（code point）。
* Unicode 14.0.0 总共定义了 144,697 个字符（character），其中也常用符号，如 Emoji 表情。
* Unicode 14.0.0 总共定义了 159 种文字（script）。
* 每个字符都有一个英文的名称，通常用 U+0020 这种表示方式。

		
## 9. Unicode 编码

* UTF-32
   - 大小头
   - 固定长度编码
* UTF-16
   - 大小头
   - 变长编码（2或者4字节）
* UTF-8
   - 大小头无关
   - 变长编码（1到6个字节）
   - ASCII 兼容

		
## 10. Unicode 码点用 UTF-16LE 编码表示

```c
static int utf16le_conv_from_uc32 (Uchar32 wc, unsigned char* mchar)
{
    Uchar16 w1, w2;

    if (wc > 0x10FFFF) {
        return 0;
    }

    if (wc < 0x10000) {
        mchar [0] = LOBYTE (wc);
        mchar [1] = HIBYTE (wc);
        return 2;
    }

    wc -= 0x10000;
    w1 = 0xD800;
    w2 = 0xDC00;

    w1 |= (wc >> 10);
    w2 |= (wc & 0x03FF);

    mchar [0] = LOBYTE (w1);
    mchar [1] = HIBYTE (w1);
    mchar [2] = LOBYTE (w2);
    mchar [3] = HIBYTE (w2);
    return 4;
}
```

注：代码选自 [MiniGUI](https://github.com/VincentWei/minigui)

		
## 11. UTF-16LE 编码转 Unicode 码点

```c
static Achar32 utf16le_get_char_value (const unsigned char* mchar, int len)
{
    Uchar16 w1, w2;
    Uchar32 wc;

    w1 = MAKEWORD16 (mchar[0], mchar[1]);

    if (w1 < 0xD800 || w1 > 0xDFFF)
        return w1;

    w2 = MAKEWORD16 (mchar[2], mchar[3]);

    wc = w1;
    wc <<= 10;
    wc |= (w2 & 0x03FF);
    wc += 0x10000;

    return wc;
}
```

注：代码选自 [MiniGUI](https://github.com/VincentWei/minigui)

		
## 12. 为什么 UTF-8 编码获得统治地位？

* Unix、C 语言作者之一肯·汤普逊提出。

```
   0x00000000 - 0x0000007F:
       0xxxxxxx

   0x00000080 - 0x000007FF:
       110xxxxx 10xxxxxx

   0x00000800 - 0x0000FFFF:
       1110xxxx 10xxxxxx 10xxxxxx

   0x00010000 - 0x001FFFFF:
       11110xxx 10xxxxxx 10xxxxxx 10xxxxxx

   0x00200000 - 0x03FFFFFF:
       111110xx 10xxxxxx 10xxxxxx 10xxxxxx 10xxxxxx

   0x04000000 - 0x7FFFFFFF:
       1111110x 10xxxxxx 10xxxxxx 10xxxxxx 10xxxxxx 10xxxxxx
```

注：UTF-8 编码现有的 Unicode 码点，最高只需要四个字节。

		
## 13. Unicode 相关算法（上）

* UAX #9：双向（bidirectional）算法
* UAX #10：定序（collation）算法
* UAX #11：东亚宽度
* UAX #14：换行算法
* UAX #15：正规化

		
## 14. Unicode 相关算法（下）

* UAX #24：文字属性
* UAX #29：文本分段
* UAX #38：汉字数据库（Unihan）
* UAX #50：垂直文本布局

		
## 15. 为何 Unicode 最终胜利？

经济基础决定上层建筑

		
## 16. Unicode 引入的混乱

* C 语言中的宽字符 `wchar_t`，不同平台宽度不一。
* Java 语言中的 16 位宽的字符。
* Python 2.0 对字符串不区分编码，使用 `#coding:xxx` 告知解释器当前默认编码。
* Python 3.0 中字符串使用 UTF-16 编码，其他编码的字符串使用字节序列。

		
## 17. 字符集/编码的转换（C）

```c
static Achar32 gb2312_0_char_to_achar32 (const unsigned char* pre_mchar,
        int pre_len, const unsigned char* cur_mchar, int cur_len)
{
    int area = cur_mchar [0] - 0xA1;
    if (area < 9) {
        return (area * 94 + cur_mchar [1] - 0xA1);
    }
    else if (area >= 15)
        return ((area - 6)* 94 + cur_mchar [1] - 0xA1);
    return 0;
}

static const unsigned short __mg_gbunicode_map[] = {
    0x3000, 0x3001, 0x3002, 0x30fb,
    0x02c9, 0x02c7, 0x00a8, 0x3003,
    0x3005, 0x2015, 0xff5e, 0x2225,
    0x2026, 0x2018, 0x2019, 0x201c,
    0x201d, 0x3014, 0x3015, 0x3008,
    ...
    0x9eea, 0x9eef, 0x9f22, 0x9f2c,
    0x9f2f, 0x9f39, 0x9f37, 0x9f3d,
    0x9f3e, 0x9f44
};

static UChar32 gb2312_0_conv_to_uc32 (AChar32 achar32)
{
    return (UChar32)__mg_gbunicode_map[achar_value];
}
```

		
## 18. 字符集/编码的转换（Python 3）

* encode()：将 Unicode 字符串编码为指定字符集/编码。
* decode()：将指定字符集编码字节序列编码为 Unicode 字符串。

```python
# coding: utf-8

str_uni = u"中文"         // 定义一个 Unicode 字符串

bytes_gbk = str_uni.encode("gbk")       // 按 GBK 编码
bytes_utf8 = str_uni.encode("utf8")     // 按 UTF8 编码

byets_gbk.decode("gbk")                 // 按 GBK 解码为 Unicode 字符串
byets_gbk.decode("utf8")                // 异常
```

		
## 19. HVML 的设计

* 字符串类型始终使用 UTF-8 编码，只保存有效的 Unicode 字符。
* 其他所有字符集/编码统一使用字节序列类型处理。
* 提供类似 Python 3 的 decode 和 encode 方法。

```js
$EJSON.fetchstr(bxFFFE000048000000560000004D0000004C0000002F6600006851000003740000969900002A4E0000EF530000167F00000B7A000007680000B08B0000ED8B0000008A000001FF0000", "utf32")
    // string: "HVML是全球首个可编程标记语言！"

$EJSON.fetchstr(bx00000048000000560000004D0000004C0000662F00005168000074030000999600004E2A000053EF00007F1600007A0B0000680700008BB000008BED00008A000000FF01, 'utf32be')
    // string: "HVML是全球首个可编程标记语言！"

$EJSON.pack("i16le:2 i32le", [10, 15], 255)
    // bsequence: bx0A000F00FF000000

$EJSON.unpack("i16le i32le", bx0a000a000000)
    // array: [10L, 10L]
```

		
## 20. 文字的其他编码形式

- Base64：只使用有限的 ASCII 字符表示二进制数据（三个字节扩展为四个字节表示）
- URL 编码： 对 `-_.` 之外的所有非字母数字字符都将被替换成百分号（%）后跟两位十六进制数，空格编码为加号（+）
- URL 裸编码：对 `-_.` 之外的所有非字母数字字符都将被替换成百分号（%）后跟两位十六进制数

```js
$EJSON.unpack("utf8", $EJSON.base64_decode('SFZNTCDmmK/lhajnkIPpppbmrL7lj6/nvJbnqIvmoIforrDor63oqIA='))
    ['HVML 是全球首款可编程标记语言']

$EJSON.unpack("utf8", $URL.rawdecode('HVML%20%E6%98%AF%E5%85%A8%E7%90%83%E9%A6%96%E6%AC%BE%E5%8F%AF%E7%BC%96%E7%A8%8B%E6%A0%87%E8%AE%B0%E8%AF%AD%E8%A8%80'))
    ['HVML 是全球首款可编程标记语言']
```

