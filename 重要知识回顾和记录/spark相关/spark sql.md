1. RDD 与 DataFrame的区别
   * RDD和DataFrame均为Spark平台对数据的一种抽象和组织方式，但是两者的地位或者说设计目的却截然不同。
   * RDD是整个Spark平台的存储、计算以及任务调度的逻辑基础，更具有通用性，适用用于各类数据源，不关注数据的内容、结构，一视同仁。
   * DataFrame是只针对结构化、数据源的高层数据抽象，其中在DataFrame对象的创建过程中必须指定数据集的结构信息(Schema)，所以DataFrame生来便是具有专用性的数据抽象，只能读取具有鲜明结构的数据集。 
   * DataFrame原先叫做SchemaRDD
   
