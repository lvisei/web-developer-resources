## 文件搜索命令 find
*避免大范围的搜索，会非常浪费系统资源，建议不在直接在`/`目录下搜索，find 搜索默认是完全匹配。*

### 格式
- `find [搜索范围] [搜索条件]`，例: `find /home -name 文件名`

### 模糊查询，常用通配符
- `*`匹配任意字符，例：`find /home -name '*test*'`，显示所有名字带test的文件；或例：`find /home -name '*'`，home目录下的所有文件，包括隐藏文件。
- `?` 匹配任意一个字符
- `[]` 匹配任意一个中括号内的字符，例：`find /home -name 'test[12]'`，显示 test1 和 test2 文件；或 `find /home -name “【12】*”` ，显示以 1 和 2 开头的文件。
- 组合使用，例：`find /home -name '*[12]'`，显示所有1和2结尾的文件

### find 搜索条件
- 按文件时间来搜索
  - `atime` 文件访问时间
  - `ctime` 改变文件属性
  - `mtime` 修改文件内容
  - `find /home -mtime +10` 在home目录下，查找10天前修改的文件
  - `find /home -mtime 10` 在home目录下，查找10天前当天修改的文件
  - `find /home -mtime -10` 在home目录下，查找10天内修改的文件
- `-iname` 不区分要搜索的大小写格式
  - `find /root -iname test  `不区分要搜索的 test 大小写格式
- 按用户搜索
  - `find /root -user root` 搜索 root 目录下的所有属于 root 用户的文件
  - `find /root -nouser` 没有所属者的文件，liunx 中，每个文件都有所属者，如果没有，那么一般都是垃圾文件，但还是有特例的，比如内核产生的文件，就没有所属者，一般在 proc 和 sys 目录下；还有外来文件，也就是U盘拷入的文件也会忽略所有者。
- `-size` 以文件大小查找，文件大小用小写k和大写M
- 多条件查找
  - `-a` 表示 and
  - `-o` 表示 or

### 对查找结果执行命令
- `-exec {} \`， 固定格式，表示直接对前面的搜索结果进行后面的命令处理
`find /etc -size +20k -a -size -50k -exec ls -lh {} \;`