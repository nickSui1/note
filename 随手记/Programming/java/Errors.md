## NullPointerException 空指针异常

- 避免空指针异常
 - 例：`booking.getCode.equals(BookingEnum.BLOCK.getCode())`,此段代码，如果对象booking的code为null，则null调用equals函数会报错空指针异常。
 - 因此，我们可以将确保有值的变量or常量去调用函数，即：`BookingEnum.BLOCK.getCode().equals(booking.getCode)`,这样枚举BLOCK的code是一定有值的，非空对象去调用equals函数，无论括号内的是null还是什么，都不会异常。

## 数据库问题汇总

### Sql链接失败

- 检查sql服务是否启用
  - 例如使用的homebrew，在终端输入 brew services list, 查看服务是否启用。

### Sql服务启动失败

- 例如postgresql启动失败，例如 brew services list显示 error 256

  - 删除pid文件，

    `rm -f /usr/local/var/postgresql@15/postmaster.pid`

    重新启动，先stop再start

    `brew services stop postgresql@15`

    `brew services start postgresql@15`