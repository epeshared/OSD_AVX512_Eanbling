# 1. Code Repository

https://github.com/oceanbase/oceanbase/compare/master...xtangxtang:master

# 2. Code calling graph
**ob_build_index_task.cpp::ObIndexLocalSortTask**::process --> **ob_partition_storage.cpp::ObPartitionStorage**::local_sort_index_by_range --> **ob_parallel_external_sort.h::ObExternalSort**::add_item --> **ob_parallel_external_sort.h::ObMemorySortRound**::build_fragment  --> **ob_avx512_sort.cpp**::sort -->**ob_avx512_sort.cpp**::sort_loop --> **ob_i_store.cpp::ObSortRow::**::get_sort_key --> **ob_object.cpp::ObObj**::make_sort_key --> **ObCharset**::sortkey_v2 --> **ob_ctype.h**::strnxfrm_v2


**ob_partition_merge_util.cpp::ObBuildIndexContext**::preallocate_local_sorters --> **ob_i_store.cpp::ObSortRow**
**ob_build_index_task.cpp::ObIndexMergeTask**::merge_local_sort_index  --> **ob_i_store.cpp::ObSortRow**
**ob_build_index_task.h::ObIndexMergeTask**::merge_local_sort_index --> **ob_i_store.cpp::ObSortRow**
**ob_partition_storage.cpp::ObPartitionStorage**::local_sort_index_by_range --> [**ob_i_store.cpp::ObSortRow**, **ob_store_row_comparer.h::ObStoreRowComparer**]

# Refined Code
### use_memcmp_

* Before refined
![image](https://user-images.githubusercontent.com/3771594/161369531-1da69158-5f44-4be8-ab50-5fa0141d1f6a.png)

* After refined
![image](https://user-images.githubusercontent.com/3771594/161371359-f7b3792f-c739-4c89-8b63-74786372e711.png)



