Introduction:

An example of thread-safe double-buffering implemented in C++

一个C++实现的线程安全的双缓冲实例

scenario: a multithreaded business system writes data to memory, while a synchronous thread reads data and synchronizes to redis.

场景：多线程的业务系统不断写入数据到内存中，同步线程不断读取数据并同步redis


Dsigning scheme：

For the business is well suited to the map structure, a two-layer map is adopted.

由于业务非常适合map的结构，因此采用了双层map

    typedef std::map<int,int> EventCountMap;
    typedef std::map<int,EventCountMap> RuleEventCountMap;

In order to achieve read/write separation, double buffering, one reading and one writing are adopted. 

Since std::map is not thread safe, it needs to be locked when reading and writing.

为了实现读写分离，采用双缓冲，一个读，一个写，由于std::map并不是线程安全的，所以读写的时候需要加锁处理。

    class BufferProc
    {
    public:
        BufferProc();
        ~BufferProc();
        int init();
        RuleEventCountMap * get();
        int set(int key, int field_key, int field_value);
        void clear();
    private:
        MutexLock buffer_map_mutex;
        RuleEventCountMap *buffer_map_A_ptr;
        RuleEventCountMap *buffer_map_B_ptr;
        RuleEventCountMap *buffer_map_producer_ptr;
        RuleEventCountMap *buffer_map_consumer_ptr;	
    };

init() is used to initialize the double buffer

init用于初始化双缓冲

get() exchange the buffer_map_producer_ptr and buffer_map_consumer_ptr,then return RuleEventCountMap *

get() 交换缓冲区的指针并返回

set(int key, int field_key, int field_value) is used for producer insert data

set用于生产者插入数据，

celar() is used to clear the buffer.

celar用于清空缓冲区。



