# 1. Code Repository

https://github.com/oceanbase/oceanbase/compare/master...xtangxtang:master

# 2. Performance

System: Intel(R) Xeon(R) Gold 6252 CPU @ 2.10GHz

![image](https://user-images.githubusercontent.com/3771594/163766722-5a322b46-b1c9-40fa-8e74-72c27a3483e3.png)


    create table lineitem (
    l_orderkey bigint not null,
    l_partkey bigint not null,
    l_suppkey bigint not null,
    l_linenumber bigint not null,
    l_quantity bigint not null,
    l_extendedprice bigint not null,
    l_discount bigint not null,
    l_tax bigint not null,
    l_returnflag char(1) default null,
    l_linestatus char(1) default null,
    l_shipdate date not null,
    l_commitdate date default null,
    l_receiptdate date default null,
    l_shipinstruct char(25) default null,
    l_shipmode char(10) default null,
    l_comment varchar(44) default null,
    primary key(l_orderkey, l_linenumber))
    tablegroup = tpch_tg_100g_lineitem_order_group
    partition by key (l_orderkey) partitions 192;
    
    create index I_L_ORDERKEY on lineitem(l_orderkey) local;
    create index I_L_SHIPINS on lineitem(l_shipinstruct) local;
    create index I_L_SHIPDATE_COMMITDATE on lineitem(l_shipdate, l_commitdate) local;
    
    drop index I_L_ORDERKEY on lineitem;
    drop index I_L_SHIPINS on lineitem;
    drop index I_L_SHIPDATE_COMMITDATE on lineitem;    
    
    
# 3. Code calling graph
**ob_build_index_task.cpp::ObIndexLocalSortTask**::process --> **ob_partition_storage.cpp::ObPartitionStorage**::local_sort_index_by_range --> **ob_parallel_external_sort.h::ObExternalSort**::add_item --> **ob_parallel_external_sort.h::ObMemorySortRound**::build_fragment  --> **ob_avx512_sort.cpp**::sort -->**ob_avx512_sort.cpp**::sort_loop --> **ob_i_store.cpp::ObStoreRow::**::get_sort_key --> **ob_object.cpp::ObObj**::make_sort_key --> **ObCharset**::sortkey_v2 --> **ob_ctype.h**::strnxfrm_v2

# 4. ObStoreRow的key的比较

ObStoreRow的value既存储了数据又存储了key.在没有使用AVX512的情况下, key的compare是在ob_obj_compare.cpp中定义的。具体比较过程是判断key的类别，然后根据具体类别来判断大小。比如对uint型比较，就是直接判断其数值大小，代码如下：

    #define DEFINE_CMP_OP_FUNC_UINT_ENUMSET(op, op_str)                          \
      template <>                                                                \
      inline int ObObjCmpFuncs::cmp_op_func<ObUIntTC, ObEnumSetTC, op>(          \
          const ObObj& obj1, const ObObj& obj2, const ObCompareCtx& /*cmp_ctx*/) \
      {                                                                          \
        OBJ_TYPE_CLASS_CHECK(obj1, ObUIntTC);                                    \
        OBJ_TYPE_CLASS_CHECK(obj2, ObEnumSetTC);                                 \
        return obj1.get_uint64() op_str obj2.get_uint64();                       \
      }

如果比较string型，就按照strcmp来比，代码如下：

    #define DEFINE_CMP_OP_FUNC_STRING_STRING(op, op_str)                                                                  \
      template <>                                                                                                         \
      inline int ObObjCmpFuncs::cmp_op_func<ObStringTC, ObStringTC, op>(                                                  \
          const ObObj& obj1, const ObObj& obj2, const ObCompareCtx& cmp_ctx)                                              \
      {                                                                                                                   \
        OBJ_TYPE_CLASS_CHECK(obj1, ObStringTC);                                                                           \
        OBJ_TYPE_CLASS_CHECK(obj2, ObStringTC);                                                                           \
        ObCollationType cs_type = cmp_ctx.cmp_cs_type_;                                                                   \
        if (CS_TYPE_INVALID == cs_type) {                                                                                 \
          if (obj1.get_collation_type() != obj2.get_collation_type() || CS_TYPE_INVALID == obj1.get_collation_type()) {   \
            LOG_ERROR("invalid collation", K(obj1.get_collation_type()), K(obj2.get_collation_type()), K(obj1), K(obj2)); \
          } else {                                                                                                        \
            cs_type = obj1.get_collation_type();                                                                          \
          }                                                                                                               \
        }                                                                                                                 \
        return CS_TYPE_INVALID != cs_type ? static_cast<int>(ObCharset::strcmpsp(cs_type,                                 \
                                                obj1.v_.string_,                                                          \
                                                obj1.val_len_,                                                            \
                                                obj2.v_.string_,                                                          \
                                                obj2.val_len_,                                                            \
                                                CALC_WITH_END_SPACE(obj1, obj2, cmp_ctx)) op_str 0)                       \
                                          : CR_OB_ERROR;  
                                          
但是对于采用avx512的bitonic的key值比较，是需要对每个字节的二进制进行比较的。明显的，上述的ObStoreRow的比较是无法使用avx512的。所以需要将所有的key的内型都转换成可以二进制比较的形式。而MyRocks就提供了一种叫memcompareble format的方法
  
## Memcomparable format

memcompareble format的主要目的就是可以不在序列号和反序列化的情况下做到直接利用C语言的memcmp的进行比较。而memcmp就是使用按照字节的二进制比较。所以memcomparable format就可以很好的支持avx512的比较。

其概念在https://github.com/facebook/mysql-5.6/wiki/MyRocks-record-format#:~:text=To%20support%20memcomparable%20format%2C%20MyRocks,remainder%20of%20non%2Dkey%20columns. 中有描述（中文翻译版：https://wanghenshui.github.io/MyRocks_zh_doc/%E4%B8%83%E3%80%81%E5%86%85%E9%83%A8%E7%BB%93%E6%9E%84/2.MyRocks%E8%AE%B0%E5%BD%95%E6%A0%BC%E5%BC%8F.html ）

* https://blog.karatos.in/a?ID=01050-75f2485f-7cf3-416b-bb60-8c868ae7f2b9

比如

![image](https://user-images.githubusercontent.com/3771594/162111330-534ff8ea-bb5b-4092-bae5-9f5a0289862e.png)

对应于MyRocks(https://github.com/facebook/mysql-5.6/blob/fb-mysql-5.6.35/storage/rocksdb/rdb_datadic.cc)的代码：

![image](https://user-images.githubusercontent.com/3771594/162111561-f7adb9f0-d32f-4e99-9119-194ccdd065d0.png)

## 加入新的函数

所以为了在创建索引的时候使用到avx512b对key的比较，在ObStoreRow中加入了get_sort_key和在ObObject中加入了make_sort_key函数。make_sort_key负责将所有内型转换成可以比较的二进制形式，然后通过get_sort_key返回。

# 5. Refined Code
### how to use avx512

* ENABLE_AVX512F is enabled in build.sh
* __builtin_cpu_supports("avx512f") is enbales in src/storage/ob_parallel_external_sort.h

![image](https://user-images.githubusercontent.com/3771594/162872922-4132b618-5589-4a99-888b-49db3ba44d27.png)


### use_memavx512_

* Before refined
![image](https://user-images.githubusercontent.com/3771594/161369531-1da69158-5f44-4be8-ab50-5fa0141d1f6a.png)

* After refined
![image](https://user-images.githubusercontent.com/3771594/162873248-609ed44c-ce58-42d6-9cdb-37f78f5f4080.png)




