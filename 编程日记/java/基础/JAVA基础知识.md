## 序列化和反序列化

### SerialVersionId

所有实现了Serializable接口的类都建议生成一个SerialVersionId，通过IDE或者手写的方式生成，**可用来验证版本的一致性**。



序列化是将对象的状态信息转换为可存储或传输的形式的过程。我们都知道，Java对象是保存在JVM的堆内存中的，也就是说，如果JVM堆不存在了，那么对象也就跟着消失了。

而序列化提供了一种方案，可以让你在即使JVM停机的情况下也能把对象保存下来的方案。就像我们平时用的U盘一样。把Java对象序列化成可存储或传输的形式（如二进制流），比如保存在文件中。这样，当再次需要这个对象的时候，从文件中读取出二进制流，再从二进制流中反序列化出对象。

**虚拟机是否允许反序列化，不仅取决于类路径和功能代码是否一致，一个非常重要的一点是两个类的序列化 ID 是否一致**，这个所谓的序列化ID，就是我们在代码中定义的`serialVersionUID`。



- SerialVersionId的定义方式：
  - 显示，手动定义或者通过IDE来快捷生成。
  - 隐式，如果未显示定义，则默认隐式生成一个SerialVersionId，在每次修改类的时候都会自动更新SerialVersionId。

- **建议显示定义SerialVersionId**, 如果未显示定义且对类进行了修改操作，反序列化的过程中，会对SerialVersionId进行比较，如果未定义SerialVersionId，在反序列化的调用链中initNonProxy类的方法getSerialVersionUID会生成一个新的默认serialVersionUID，会导致反序列化报错InvalidClassException。

- 显示定义SerialVersionId的好处是可以灵活控制类的版本，做兼容性升级的时候，不改变SerialVersionId的值。

  做非兼容性升级的时候，改变SerialVersionId的值，这样可以避免序列化混乱。



