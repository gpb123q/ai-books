# 分区

启动管道时，StreamSets Transformer将启动Spark应用程序。与运行其他任何应用程序一样，Spark运行该应用程序，将管道数据拆分为多个分区，并在分区上并行执行操作。

Spark自动为您处理管道数据的分区。但是，有时您可能需要更改分区的大小和数量。

## 初始分区

Spark根据流水线的来源确定如何将流水线数据拆分为初始分区。Spark使用这些分区来处理来自原始位置的输出流，除非处理器使Spark改组数据。

Spark根据原始类型确定如何初始将管道数据拆分为分区。例如，对于基于文件的来源，分区由读取的文件数和文件类型确定。对于JDBC起源，要使用的分区数由stage属性定义。

有关来源如何创建分区的信息，请参阅来源文档中的“分区”。

## 改组

某些处理器会导致Spark改组数据，重新分配数据，以便在分区之间对数据进行不同的分组。整理数据时，Spark通常会增加分区数。

以下处理器可能会导致Spark随机播放数据：

- 聚合处理器
- 加入处理器
- 排名处理器
- 分区处理器

改组可能是一项昂贵的操作，因此为了获得最佳性能，请设计管道以使用尽可能少的导致改组的处理器。

整理数据时，Spark将创建`spark.sql.shuffle.partitions`属性中定义的最小分区数 。默认情况下，该属性设置为200个分区。因此，Spark通常在对数据进行混排时会创建空分区。当管道写入文件目标时，Spark使用这些空分区之一来写入一个不包含数据的额外文件。

**提示：**`spark.sql.shuffle.partitions`通过将属性添加为管道的额外Spark配置，可以更改特定管道的属性的默认值 。Spark在运行管道时使用定义的值。

## 重新分区

当您需要更改分区时，请在管道中使用“分区”处理器。

在[重新分区处理器](https://streamsets.com/documentation/controlhub/latest/help/transformer/Processors/Repartition.html#concept_cm5_lfg_wgb) 改变如何管线数据进行分区。处理器在分区之间重新分配数据，根据需要增加或减少分区的数量。处理器可以跨分区随机重新分配数据，也可以按指定的字段重新分配数据。

出于以下原因，您可能使用Repartition处理器对管道数据进行重新分区：

- 重新分布已倾斜或在分区之间分布不均的数据。
- 当管道由于内存不足错误而失败时，请增加分区数。
- 更改写入文件系统的分区数。
- 按字段对数据进行分区，以提高下游分析查询的性能。