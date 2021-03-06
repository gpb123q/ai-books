# 常用表达

## 正则表达式概述

正则表达式，也称为regex，描述了字符串的模式。

本附录提供了一些有关将正则表达式与Data Collector一起使用的详细信息和技巧。有关如何定义正则表达式的完整说明，请使用手册或在线参考，例如：[https](https://docs.oracle.com/javase/tutorial/essential/regex/index.html) : [//docs.oracle.com/javase/tutorial/essential/regex/index.html](https://docs.oracle.com/javase/tutorial/essential/regex/index.html)。

对于测试正则表达式，您可能会发现以下网站有用：[https](https://regex101.com/) : [//regex101.com/](https://regex101.com/)。

## 管道中的正则表达式

尽管通常不需要，但是您可以在管道中的各个位置使用基于Java的正则表达式来定义，搜索或操作字符串。

例如，在Field Masker处理器中，可以使用固定长度，可变长度或自定义静态掩码来掩码字段中的数据。如果这对您不起作用，则可以使用正则表达式定义特定的自定义蒙版。同样，如果不使用列出的格式之一，则可以使用正则表达式定义日志行的格式。

下表描述了如何在管道中使用正则表达式的一些示例：

| 位置                               | 描述                                                         |
| :--------------------------------- | :----------------------------------------------------------- |
| 目录来源                           | （可选）用于定义文件名的模式。                               |
| 文件尾源                           | 当您使用文件匹配模式命名选项时，用于定义$ {PATTERN}常量。用于定义日志行或文本的结构。 |
| 处理日志数据的来源日志解析器处理器 | （可选）用于定义日志行的模式。                               |
| 场掩蔽处理器                       | （可选）用于定义场掩码。                                     |
| regexCapture函数                   | 用于定义字符串的组和模式，以便您可以指定要返回的组。         |
| replaceAll函数                     | 用于定义要替换的字符串。                                     |

## 快速参考

下表包含一些详细信息，这些详细信息在创建正则表达式时可能会有所帮助：

| 字符 | 描述                                                         | 例子                                                         |
| :--- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| []   | 使用方括号定义字符类。                                       | [0-9] [0-9] [0-9]表示3个数字，范围从0到9（含0和9）。         |
| --   | 使用连字符定义范围。                                         | [az]定义了一个从a到z的小写字母。[AZ]定义了一个从A到Z的大写字母。 |
| \|   | 指示要定义的字符或组的替代选项。                             | [az \| AZ]表示单个大写或小写字母。                           |
| （） | 使用括号可以创建组，原子组或环顾四周。                       | （[0-9] [0-9] [0-9]）（-\|。）（[0-9] [0-9] [0-9]）（-\|。）（[0-9] [0-9] [0-9] [0-9]）表示电话号码，其区号由短划线或句点分隔，如下所示：415-555-5555和415.555.5555。 |
| <>   | 使用尖括号定义命名的捕获组。使用以下语法：`(? ...) `设置命名字段提取。 |                                                              |
| ^    | 使用克拉来否定角色类别。                                     | [^ AG]定义的字符不是A到G的大写字母。                         |
| 。   | 一个通配符，表示除换行符或其他特殊字符外的任何单个字符。     |                                                              |
| &&   | 使用两个“与”号表示两个范围的并集。                           | [\ w && [^ 1-9]代表除1-9之外的所有单词字符。                 |
| ？   | 表示零个或一个先前字符或组实例的量词。                       | B-7表示B7或B-7。                                             |
| +    | 表示前一个字符或组的一个或多个实例的量词。                   | （[[0-9] [0-9] [0-9]）-+（[0-9] [0-9] [0-9] [0-9]）代表区号为415-的电话号码555-5555和555-5555。 |
| *    | 表示零个或多个先前字符或组实例的量词。                       | （[0-9] [0-9] [0-9] [0-9] [0-9]）-（[0-9] [0-9] [0-9] [0-9]） *代表5位数和9位数的邮政编码。 |
| \    | 使用反斜杠作为转义字符。                                     |                                                              |
| \\   | 代表一个反斜杠                                               |                                                              |
| \ w  | 代表文字字符-包括字母数字字符和下划线。                      | \ w \ w \ w- \ w \ w \ w \ w \ w可以代表错误代码，例如SVR-30243。 |
| \ W  | 表示非单词字符-包括除字母数字字符和下划线以外的所有字符。    |                                                              |
| \ d  | 代表一个数字。0-9的简写。                                    | （\ d \ d \ d \ d \ d）-（\ d \ d \ d \ d）*代表9位数的邮政编码。 |
| \ D  | 表示一个非数字字符。                                         | 在任何情况下，\ D && \ S代表字母。                           |
| \ s  | 表示空白字符-包括空格，制表符，换行符和换页符。              |                                                              |
| \ S  | 表示一个非空格字符-包括除空格，制表符，换行符和换页符之外的所有字符。 |                                                              |
| \ t  | 制表符。                                                     |                                                              |
| \ r  | 返回字符。                                                   |                                                              |
| \ n  | 换行符或换行符。                                             |                                                              |
| \F   | 换页字符。                                                   |                                                              |

## 正则表达式示例

- 屏蔽信用卡号（一组除外）

  您可以在Field Masker处理器中使用以下正则表达式来屏蔽信用卡或借记卡中的所有数字（最后4位数字除外）：`(.*)([0-9]{4})`

  此正则表达式定义了两个组。第一组`.*`用来表示任意数量的数字。第二组代表最后四位数字。在掩码配置中，将“ **要显示**的**组”**属性设置为2，以使输出数据显示第二个组，从而显示信用卡号的后4位。

  以下正则表达式执行相同的任务：`(\d*)(\d{4}) ``(\d*)(\d\d\d\d)`