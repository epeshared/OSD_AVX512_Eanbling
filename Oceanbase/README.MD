# 1. Code Repository

https://github.com/oceanbase/oceanbase/compare/master...xtangxtang:master

# Performance

![image](https://user-images.githubusercontent.com/3771594/163765268-69de0197-487f-4fc6-af77-4f6085df140e.png)

    create index I_L_ORDERKEY on lineitem(l_orderkey) local;
    create index I_L_SHIPINS on lineitem(l_shipinstruct) local;
    create index I_L_SHIPDATE_COMMITDATE on lineitem(l_shipdate, l_commitdate) local;
    
    
# Code calling graph
**ob_build_index_task.cpp::ObIndexLocalSortTask**::process --> **ob_partition_storage.cpp::ObPartitionStorage**::local_sort_index_by_range --> **ob_parallel_external_sort.h::ObExternalSort**::add_item --> **ob_parallel_external_sort.h::ObMemorySortRound**::build_fragment  --> **ob_avx512_sort.cpp**::sort -->**ob_avx512_sort.cpp**::sort_loop --> **ob_i_store.cpp::ObStoreRow::**::get_sort_key --> **ob_object.cpp::ObObj**::make_sort_key --> **ObCharset**::sortkey_v2 --> **ob_ctype.h**::strnxfrm_v2


# Memcomparableformat

ObObj::make_sort_key会用到 memcomparableformat, 其概念在https://github.com/facebook/mysql-5.6/wiki/MyRocks-record-format#:~:text=To%20support%20memcomparable%20format%2C%20MyRocks,remainder%20of%20non%2Dkey%20columns. 中有描述（中文翻译版：https://wanghenshui.github.io/MyRocks_zh_doc/%E4%B8%83%E3%80%81%E5%86%85%E9%83%A8%E7%BB%93%E6%9E%84/2.MyRocks%E8%AE%B0%E5%BD%95%E6%A0%BC%E5%BC%8F.html ）

* https://blog.karatos.in/a?ID=01050-75f2485f-7cf3-416b-bb60-8c868ae7f2b9

比如

![image](https://user-images.githubusercontent.com/3771594/162111330-534ff8ea-bb5b-4092-bae5-9f5a0289862e.png)

对应于MyRocks(https://github.com/facebook/mysql-5.6/blob/fb-mysql-5.6.35/storage/rocksdb/rdb_datadic.cc)的代码：

![image](https://user-images.githubusercontent.com/3771594/162111561-f7adb9f0-d32f-4e99-9119-194ccdd065d0.png)


# Refined Code
### how to use avx512

* ENABLE_AVX512F is enabled in build.sh
* __builtin_cpu_supports("avx512f") is enbales in src/storage/ob_parallel_external_sort.h

![image](https://user-images.githubusercontent.com/3771594/162872922-4132b618-5589-4a99-888b-49db3ba44d27.png)


### use_memavx512_

* Before refined
![image](https://user-images.githubusercontent.com/3771594/161369531-1da69158-5f44-4be8-ab50-5fa0141d1f6a.png)

* After refined
![image](https://user-images.githubusercontent.com/3771594/162873248-609ed44c-ce58-42d6-9cdb-37f78f5f4080.png)




