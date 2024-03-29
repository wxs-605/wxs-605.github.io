# K v Database


梳理一下大二时做的一个课程设计，用C++语言实现一个轻量级的小型数据库，只存储键值对。

特点：
- 依赖少：实现过程中涉及到的数据结构如哈希表、小根堆等，均是自己实现，不依赖第三方库文件；
- 高效： 以顺序写的方式将数据写入到磁盘文件中，文件读取速度快。由于成本原因，大部分情况我们使用的都是传统的机械硬盘，机械硬盘随机读写慢，但是顺序读写快；
- 支持基本的增删改查、同时支持设置数据的生存时间。

**键值对的设计**
对于K-V中存储的每个key-value，每个记录有5个字段组成：

1. key-length: 定长四节点的(int)整形表示，记录的是key的长度；

2. value-length: 定长四个节点的(int)整形表示，记录的是value的长度；

3. operation：定长双字节的(short)整形表示，1表示SET操作，2表示DELETE操作，3表示EXPIRES操作；

3. key: 记录的是key的字符串；

4. value: 记录的是value的字符串；

**运用到的数据结构**

- 哈希表
用哈希表建立内存索引，哈希表中的节点存储有每个记录的key、记录的过期时间及记录在文件中的偏移值。
- 小根堆
用小根堆维护被设置了过期时间的记录，堆中的节点同样存储了记录的key、记录的过期时间及记录在文件中的偏移值。

**基本操作**
- 写操作(SET)
1. 将新的Key-Value Record追加写入到文件的末尾；

2. 将 K-V Record同步更新到哈希表中，若Key不存在，则将它添加到索引中。若Key已经存在，则修改索引指向的位置(既再文件中偏移量)；


- 删除操作(DELETE)
1. 在文件的末尾追加写入一个K-V Record，该Record中value的长度为-1，操作类型字段为2，表示删除这个记录；

2. 更新哈希表中对应的记录，释放被删除的记录所在的节点；


- 读操作(GET)
1. 查找哈希表，如果该记录存在，可以得到其在文件中的偏移值；

2. 如果记录存在，依据哈希表中的偏移值去文件中读出对应的value并返回；


- 过期操作(EXPIRES)
设置Key的生存周期，倒计时归零后，自动将Key删除

1. 获取当前的时间戳，加上生成时间得到过期时间戳；

2. 将这个记录追加写入到文件中，operation操作字段为3，表示设置生成时间，同时value字段用来存放过期时间戳；


*注意：*
使用小根堆记录所有Key的过期时间：
1. 读操作(GET)前，遍历堆顶元素，将所有已过期的Key删除；

2. 对重复设置过期时间的Key，需要更新小根堆中的过期时间；

3. 对已设置过期时间的Key，过期前执行删除(DELETE)或者覆盖(SET)操作，需要删除小根堆中的过期时间；


- 其他点
1. 异常处理，如打开数据库文件时，需要判断输入的文件路径是否合法、文件是否存在、及是否创建成功；

2. 由于数据库中对记录的操作都会在文件中追加如入，所以数据库文件存在一段时间后，会产生大量的无效数据，因此，当文件容量达到一定值时，需要对文件进行PURGE操作，删除无效的、过期的记录；


- 思考-进一步优化
由于磁盘操作比内存操作要慢，故可在内存中短暂存储短期内操作的数据，待内存使用达到上限阈值时，将最久不使用的Key-Value写入磁盘。


**项目中涉及的八股文**

_堆排序_  你能说一下**堆排序**吗？

堆排序主要分为两个步骤，一是建堆，而是调整然后排序，下面以小根堆为例说下这两个过程；
1. 建堆：堆中的数据是以数组的方式存储的，支持随机访问，堆中的数据有以下关系：下标从零开始，第i个节点的左孩子是2*i+1，右孩子是2*i+2，第i个节点的父节点是i/2(i为奇数)，或者是i/2 -1(i为偶数)。建堆时从下标为数组长度一般的元素开始，如果其左右孩子中有一个比它更小，那么将其与父节点交换，然后再以被交换的孩子节点作为父节点，看它的孩子中有没有比它更小的，如果有，则继续交换，重复上面的过程，直至其左右孩子不存在；接着坐标往前移，重复上述的调整过程，直到移动到第0个节点，此时整个堆构建完毕。

2. 排序：交换堆顶元素与堆中的最后一个元素，数组长度减一，然后调整堆顶元素进，与最小的孩子进行交换，交换之后再以最小的孩子作为父节点，继续与它的孩子交换，直至不能再交换。再重复2的过程，直到数组的长度为1，此时可以得到降序排序的数组；

3. 复杂度，建堆过程中，时间复杂度为O(N)，调整过程中，时间复杂度为O(NlogN)，所以总的时间复杂度为O(NlogN)；

_哈希表_

- 哈希表的**实现方式**有什么？

1. 数组+二叉搜索树：在每个哈希桶中使用二叉搜索树来存储元素。当哈希桶中的元素较多时，查询效率高，适合处理较大规模的数据。

2. 数组+链表：在每个哈希桶中使用链表来存储元素，元素的插入和删除效率高，，但是查询时间较慢，适合处理小规模的数据。

- 哈希表的一般长度是多少？为什么是这么多？

默认数组长度是 16，只要是2的次幂都行。那为什么数组长度得是 2 的次幂呢？一般来说，我们常用的 Hash函数是这样的：index = HashCode(key) % Length，由于位运算的效率比较高，所以 HashMap 就相应的可以改成：index = HashCode(key) & (Length - 1)。为了保证根据上述公式计算出来的 index 值是分布均匀的，我们就必须保证 Length 是 2 的次幂。

- 哈希表为什么按照**两倍**扩容？

因为哈希表默认的数组长度一般是2的次幂，所以按照两倍扩容之后，数组的长度仍然是2的次幂。那为什么一定是2的次幂呢？因为这样可以使元素均匀的散布hashmap中，减少hash碰撞。index的计算可以改为用位运算的方式实现，计算效率高，而且扩容后元素在新表中的位置要么不动，要么有规律的出现在新表中（二的幂次方偏移量），这样会使扩容的效率大大提高。

- 处理哈希冲突的办法有哪些？

1. 开放地址法：有线性探测法和平方探测法，当发生冲突时，继续往后找；

2. 多重哈希法：构造多个哈希函数，发生冲突后使用下一个哈希函数；

3. 链地址法：将哈希值相同的记录用链表串起来；

4. 建立公共溢出区：将哈希表分为基础表和益处表两部分，发生冲突的填入益处表；





