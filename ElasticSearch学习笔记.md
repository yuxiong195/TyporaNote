概念
1.1 Document
定义: 可以被搜索数据的最小单位。

特性：文档会被序列化成Json格式，每个文档都有一个Uid，每个文档的id是独一无二的。

Metadata元数据：标注文档的相关信息

_index: 文档所属的索引名称

_type:文档所属类型名称

_id:文档唯一ID

_source:文档原始的json数量？ ->现在项目中没有用到

_version:版本信息可以解决部分冲突问题

_score:相关性打分
