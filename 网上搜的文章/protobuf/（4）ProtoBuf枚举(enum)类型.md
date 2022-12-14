# [（四）ProtoBuf枚举(enum)类型](https://www.cnblogs.com/infodriven/p/16246629.html)

当需要定义一个消息类型的时候，可能想为一个字段指定“预定义值序列”中的一个值，这时候可以通过枚举实现。

例子：

```typescript
syntax = "proto3";//指定版本信息，不指定会报错

enum PhoneType //枚举消息类型，使用enum关键词定义,一个电话类型的枚举类型
{
    MOBILE = 0; //proto3版本中，首成员必须为0，成员不应有相同的值
    HOME = 1;
    WORK = 2;
}

// 定义一个电话消息
message PhoneNumber
{
    string number = 1; // 电话号码字段
    PhoneType type = 2; // 电话类型字段，电话类型使用PhoneType枚举类型
}
```

分类: [Protobuf](https://www.cnblogs.com/infodriven/category/2151382.html)