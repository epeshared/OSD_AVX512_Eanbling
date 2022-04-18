# 1. Code Repository

https://github.com/oceanbase/oceanbase/compare/master...xtangxtang:master

# Performance

![image](https://user-images.githubusercontent.com/3771594/163765268-69de0197-487f-4fc6-af77-4f6085df140e.png)

## detail

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


drop index I_L_ORDERKEY on lineitem;
drop index I_L_SHIPINS on lineitem;
drop index I_L_SHIPDATE_COMMITDATE on lineitem;

* avx512f 

MySQL [ssb_30g]> create index I_L_ORDERKEY on lineitem(l_orderkey) local; <br>
Query OK, 0 rows affected (54.981 sec) <br>

MySQL [ssb_30g]> select  * from lineitem order by l_orderkey  limit 10;<br>
+------------+-----------+-----------+--------------+------------+-----------------+------------+-------+--------------+--------------+------------+--------------+---------------+-------------------+------------+-------------------------------------+ <br>
| l_orderkey | l_partkey | l_suppkey | l_linenumber | l_quantity | l_extendedprice | l_discount | l_tax | l_returnflag | l_linestatus | l_shipdate | l_commitdate | l_receiptdate | l_shipinstruct    | l_shipmode | l_comment                           | <br>
+------------+-----------+-----------+--------------+------------+-----------------+------------+-------+--------------+--------------+------------+--------------+---------------+-------------------+------------+-------------------------------------+ <br>
|          1 |   2019273 |    219274 |            2 |         36 |           42918 |          0 |     0 | N            | O            | 1996-04-12 | 1996-02-28   | 1996-04-20    | TAKE BACK RETURN  | MAIL       | ly final dependencies: slyly bold   | <br>
|          1 |     63945 |    138946 |            4 |         28 |           53450 |          0 |     0 | N            | O            | 1996-04-21 | 1996-03-30   | 1996-05-16    | NONE              | AIR        | lites. fluffily even de             | <br>
|          1 |    469034 |     19037 |            6 |         32 |           32096 |          0 |     0 | N            | O            | 1996-01-30 | 1996-02-07   | 1996-02-03    | DELIVER IN PERSON | MAIL       | arefully slyly ex                   |<br>
|          1 |    720800 |     45807 |            5 |         24 |           43698 |          0 |     0 | N            | O            | 1996-03-30 | 1996-03-14   | 1996-04-01    | NONE              | FOB        |  pending foxes. slyly re            | <br>
|          1 |   1910994 |    110995 |            3 |          8 |           16039 |          0 |     0 | N            | O            | 1996-01-29 | 1996-03-05   | 1996-01-31    | TAKE BACK RETURN  | REG AIR    | riously. regular, express dep       |<br>
|          1 |   4655681 |    230697 |            1 |         17 |           27820 |          0 |     0 | N            | O            | 1996-03-13 | 1996-02-12   | 1996-03-22    | DELIVER IN PERSON | TRUCK      | egular courts above the             |<br>
|          2 |   3185092 |     35113 |            1 |         38 |           44724 |          0 |     0 | N            | O            | 1997-01-28 | 1997-01-14   | 1997-02-02    | TAKE BACK RETURN  | RAIL       | ven requests. deposits breach a     |<br>
|          3 |   3853447 |    103472 |            3 |         27 |           37807 |          0 |     0 | A            | F            | 1994-01-16 | 1993-11-22   | 1994-01-23    | DELIVER IN PERSON | SHIP       | nal foxes wake.                     |<br>
|          3 |    571063 |    196067 |            2 |         49 |           55568 |          0 |     0 | R            | F            | 1993-11-09 | 1993-12-20   | 1993-11-24    | TAKE BACK RETURN  | RAIL       |  unusual accounts. eve              |<br>
|          3 |    128909 |     53910 |            1 |         45 |           87206 |          0 |     0 | R            | F            | 1994-02-02 | 1994-01-04   | 1994-02-23    | NONE              | AIR        | ongside of the furiously brave acco |<br>
+------------+-----------+-----------+--------------+------------+-----------------+------------+-------+--------------+--------------+------------+--------------+---------------+-------------------+------------+-------------------------------------+<br>
10 rows in set (0.158 sec)<br>


MySQL [ssb_30g]> create index I_L_SHIPINS on lineitem(l_shipinstruct) local;
Query OK, 0 rows affected (1 min 28.396 sec)


MySQL [ssb_30g]> select  * from lineitem order by l_shipinstruct  limit 10;
+------------+-----------+-----------+--------------+------------+-----------------+------------+-------+--------------+--------------+------------+--------------+---------------+----------------+------------+--------------------------------------------+
| l_orderkey | l_partkey | l_suppkey | l_linenumber | l_quantity | l_extendedprice | l_discount | l_tax | l_returnflag | l_linestatus | l_shipdate | l_commitdate | l_receiptdate | l_shipinstruct | l_shipmode | l_comment                                  |
+------------+-----------+-----------+--------------+------------+-----------------+------------+-------+--------------+--------------+------------+--------------+---------------+----------------+------------+--------------------------------------------+
|        836 |   5637444 |    162499 |            1 |          6 |            8287 |          0 |     0 | N            | O            | 1996-12-09 | 1997-01-31   | 1996-12-29    | COLLECT COD    | TRUCK      | fully bold theodolites are daringly across |
|       3591 |   2046939 |     96952 |            2 |         24 |           45260 |          0 |     0 | R            | F            | 1993-12-26 | 1994-01-07   | 1994-01-25    | COLLECT COD    | FOB        | ages. slyly regular dependencies cajo      |
|       3591 |   4575675 |     75676 |            4 |         49 |           85772 |          0 |     0 | A            | F            | 1994-03-21 | 1994-01-26   | 1994-03-28    | COLLECT COD    | AIR        |  mold slyly. bl                            |
|       4064 |   5899618 |     49657 |            3 |         32 |           51754 |          0 |     0 | N            | O            | 1997-01-14 | 1997-01-01   | 1997-01-21    | COLLECT COD    | REG AIR    | es boost. careful                          |
|       6112 |    460159 |     85163 |            4 |         24 |           26859 |          0 |     0 | R            | F            | 1994-12-17 | 1994-11-12   | 1994-12-18    | COLLECT COD    | REG AIR    | ending, pending                            |
|       6112 |   5883380 |     33419 |            5 |         32 |           43619 |          0 |     0 | R            | F            | 1994-10-30 | 1994-10-31   | 1994-11-12    | COLLECT COD    | AIR        | ies cajole furiously ab                    |
|       6788 |   1552494 |    202505 |            2 |          4 |            6186 |          0 |     0 | N            | O            | 1995-08-06 | 1995-09-17   | 1995-08-29    | COLLECT COD    | TRUCK      | nding deposits; quickly regular fo         |
|       6788 |   4761673 |    261674 |            3 |          8 |           13876 |          0 |     0 | N            | O            | 1995-11-18 | 1995-09-12   | 1995-11-23    | COLLECT COD    | MAIL       | aggle blithely pending gifts. blithely     |
|       6823 |   4013650 |    263677 |            1 |          8 |           12508 |          0 |     0 | A            | F            | 1994-08-06 | 1994-09-27   | 1994-08-07    | COLLECT COD    | REG AIR    | r theodolites use slyly. b                 |
|       7234 |   1681762 |    181763 |            1 |          4 |            6975 |          0 |     0 | N            | O            | 1998-10-06 | 1998-08-25   | 1998-10-18    | COLLECT COD    | MAIL       | dinos sublat                               |
+------------+-----------+-----------+--------------+------------+-----------------+------------+-------+--------------+--------------+------------+--------------+---------------+----------------+------------+--------------------------------------------+
10 rows in set (0.415 sec)


MySQL [ssb_30g]> create index I_L_SHIPDATE_COMMITDATE on lineitem(l_shipdate, l_commitdate) local;
Query OK, 0 rows affected (1 min 17.396 sec)

MySQL [ssb_30g]> select  * from lineitem order by l_shipdate,l_commitdate  limit 10;
+------------+-----------+-----------+--------------+------------+-----------------+------------+-------+--------------+--------------+------------+--------------+---------------+-------------------+------------+------------------------------------------+
| l_orderkey | l_partkey | l_suppkey | l_linenumber | l_quantity | l_extendedprice | l_discount | l_tax | l_returnflag | l_linestatus | l_shipdate | l_commitdate | l_receiptdate | l_shipinstruct    | l_shipmode | l_comment                                |
+------------+-----------+-----------+--------------+------------+-----------------+------------+-------+--------------+--------------+------------+--------------+---------------+-------------------+------------+------------------------------------------+
|   60137444 |    574812 |    274813 |            3 |         19 |           35849 |          0 |     0 | A            | F            | 1992-01-02 | 1992-01-31   | 1992-01-13    | COLLECT COD       | SHIP       | cial, express tithes i                   |
|  121365347 |   4101510 |    126550 |            2 |         38 |           57430 |          0 |     0 | R            | F            | 1992-01-02 | 1992-01-31   | 1992-01-09    | DELIVER IN PERSON | TRUCK      | wly ironic courts above t                |
|   15413986 |   5397809 |    297810 |            4 |         14 |           26692 |          0 |     0 | A            | F            | 1992-01-02 | 1992-01-31   | 1992-01-04    | COLLECT COD       | TRUCK      | structions. blithely pending asymptotes  |
|   26696039 |   2634716 |    159741 |            2 |         50 |           82529 |          0 |     0 | R            | F            | 1992-01-02 | 1992-01-31   | 1992-01-13    | DELIVER IN PERSON | REG AIR    | . furiously regul                        |
|   46646336 |    912361 |    162368 |            1 |         44 |           60426 |          0 |     0 | A            | F            | 1992-01-02 | 1992-01-31   | 1992-01-05    | NONE              | TRUCK      | heaves. furio                            |
|   10359200 |   3057020 |     57021 |            6 |         42 |           41029 |          0 |     0 | R            | F            | 1992-01-02 | 1992-01-31   | 1992-01-25    | COLLECT COD       | FOB        |  blithely regu                           |
|  167299810 |    331144 |    256148 |            2 |         15 |           17627 |          0 |     0 | A            | F            | 1992-01-02 | 1992-01-31   | 1992-01-03    | COLLECT COD       | FOB        | ernes; furiously expre                   |
|   82801346 |   3243073 |    243074 |            2 |         36 |           36573 |          0 |     0 | A            | F            | 1992-01-02 | 1992-01-31   | 1992-01-11    | TAKE BACK RETURN  | AIR        | unts. carefully even p                   |
|  129556928 |   1521254 |     21255 |            1 |         44 |           56108 |          0 |     0 | R            | F            | 1992-01-02 | 1992-01-31   | 1992-01-04    | TAKE BACK RETURN  | REG AIR    | across the regula                        |
|   31272229 |   3321328 |    246362 |            1 |          3 |            4047 |          0 |     0 | R            | F            | 1992-01-02 | 1992-02-01   | 1992-01-31    | COLLECT COD       | AIR        | l ideas alon                             |
+------------+-----------+-----------+--------------+------------+-----------------+------------+-------+--------------+--------------+------------+--------------+---------------+-------------------+------------+------------------------------------------+
10 rows in set (0.431 sec)

* non-avx512f
MySQL [ssb_30g]>  create index I_L_ORDERKEY on lineitem(l_orderkey) local;
Query OK, 0 rows affected (1 min 6.396 sec)

MySQL [ssb_30g]> select  * from lineitem order by l_orderkey  limit 10;
+------------+-----------+-----------+--------------+------------+-----------------+------------+-------+--------------+--------------+------------+--------------+---------------+-------------------+------------+-------------------------------------+
| l_orderkey | l_partkey | l_suppkey | l_linenumber | l_quantity | l_extendedprice | l_discount | l_tax | l_returnflag | l_linestatus | l_shipdate | l_commitdate | l_receiptdate | l_shipinstruct    | l_shipmode | l_comment                           |
+------------+-----------+-----------+--------------+------------+-----------------+------------+-------+--------------+--------------+------------+--------------+---------------+-------------------+------------+-------------------------------------+
|          1 |   2019273 |    219274 |            2 |         36 |           42918 |          0 |     0 | N            | O            | 1996-04-12 | 1996-02-28   | 1996-04-20    | TAKE BACK RETURN  | MAIL       | ly final dependencies: slyly bold   |
|          1 |     63945 |    138946 |            4 |         28 |           53450 |          0 |     0 | N            | O            | 1996-04-21 | 1996-03-30   | 1996-05-16    | NONE              | AIR        | lites. fluffily even de             |
|          1 |    469034 |     19037 |            6 |         32 |           32096 |          0 |     0 | N            | O            | 1996-01-30 | 1996-02-07   | 1996-02-03    | DELIVER IN PERSON | MAIL       | arefully slyly ex                   |
|          1 |    720800 |     45807 |            5 |         24 |           43698 |          0 |     0 | N            | O            | 1996-03-30 | 1996-03-14   | 1996-04-01    | NONE              | FOB        |  pending foxes. slyly re            |
|          1 |   1910994 |    110995 |            3 |          8 |           16039 |          0 |     0 | N            | O            | 1996-01-29 | 1996-03-05   | 1996-01-31    | TAKE BACK RETURN  | REG AIR    | riously. regular, express dep       |
|          1 |   4655681 |    230697 |            1 |         17 |           27820 |          0 |     0 | N            | O            | 1996-03-13 | 1996-02-12   | 1996-03-22    | DELIVER IN PERSON | TRUCK      | egular courts above the             |
|          2 |   3185092 |     35113 |            1 |         38 |           44724 |          0 |     0 | N            | O            | 1997-01-28 | 1997-01-14   | 1997-02-02    | TAKE BACK RETURN  | RAIL       | ven requests. deposits breach a     |
|          3 |   3853447 |    103472 |            3 |         27 |           37807 |          0 |     0 | A            | F            | 1994-01-16 | 1993-11-22   | 1994-01-23    | DELIVER IN PERSON | SHIP       | nal foxes wake.                     |
|          3 |    571063 |    196067 |            2 |         49 |           55568 |          0 |     0 | R            | F            | 1993-11-09 | 1993-12-20   | 1993-11-24    | TAKE BACK RETURN  | RAIL       |  unusual accounts. eve              |
|          3 |    128909 |     53910 |            1 |         45 |           87206 |          0 |     0 | R            | F            | 1994-02-02 | 1994-01-04   | 1994-02-23    | NONE              | AIR        | ongside of the furiously brave acco |
+------------+-----------+-----------+--------------+------------+-----------------+------------+-------+--------------+--------------+------------+--------------+---------------+-------------------+------------+-------------------------------------+
10 rows in set (0.210 sec)


MySQL [ssb_30g]>  create index I_L_SHIPINS on lineitem(l_shipinstruct) local;
Query OK, 0 rows affected (2 min 47.557 sec)

MySQL [ssb_30g]> select  * from lineitem order by l_shipinstruct  limit 10;
+------------+-----------+-----------+--------------+------------+-----------------+------------+-------+--------------+--------------+------------+--------------+---------------+----------------+------------+--------------------------------------------+
| l_orderkey | l_partkey | l_suppkey | l_linenumber | l_quantity | l_extendedprice | l_discount | l_tax | l_returnflag | l_linestatus | l_shipdate | l_commitdate | l_receiptdate | l_shipinstruct | l_shipmode | l_comment                                  |
+------------+-----------+-----------+--------------+------------+-----------------+------------+-------+--------------+--------------+------------+--------------+---------------+----------------+------------+--------------------------------------------+
|        836 |   5637444 |    162499 |            1 |          6 |            8287 |          0 |     0 | N            | O            | 1996-12-09 | 1997-01-31   | 1996-12-29    | COLLECT COD    | TRUCK      | fully bold theodolites are daringly across |
|       3591 |   2046939 |     96952 |            2 |         24 |           45260 |          0 |     0 | R            | F            | 1993-12-26 | 1994-01-07   | 1994-01-25    | COLLECT COD    | FOB        | ages. slyly regular dependencies cajo      |
|       3591 |   4575675 |     75676 |            4 |         49 |           85772 |          0 |     0 | A            | F            | 1994-03-21 | 1994-01-26   | 1994-03-28    | COLLECT COD    | AIR        |  mold slyly. bl                            |
|       4064 |   5899618 |     49657 |            3 |         32 |           51754 |          0 |     0 | N            | O            | 1997-01-14 | 1997-01-01   | 1997-01-21    | COLLECT COD    | REG AIR    | es boost. careful                          |
|       6112 |    460159 |     85163 |            4 |         24 |           26859 |          0 |     0 | R            | F            | 1994-12-17 | 1994-11-12   | 1994-12-18    | COLLECT COD    | REG AIR    | ending, pending                            |
|       6112 |   5883380 |     33419 |            5 |         32 |           43619 |          0 |     0 | R            | F            | 1994-10-30 | 1994-10-31   | 1994-11-12    | COLLECT COD    | AIR        | ies cajole furiously ab                    |
|       6788 |   1552494 |    202505 |            2 |          4 |            6186 |          0 |     0 | N            | O            | 1995-08-06 | 1995-09-17   | 1995-08-29    | COLLECT COD    | TRUCK      | nding deposits; quickly regular fo         |
|       6788 |   4761673 |    261674 |            3 |          8 |           13876 |          0 |     0 | N            | O            | 1995-11-18 | 1995-09-12   | 1995-11-23    | COLLECT COD    | MAIL       | aggle blithely pending gifts. blithely     |
|       6823 |   4013650 |    263677 |            1 |          8 |           12508 |          0 |     0 | A            | F            | 1994-08-06 | 1994-09-27   | 1994-08-07    | COLLECT COD    | REG AIR    | r theodolites use slyly. b                 |
|       7234 |   1681762 |    181763 |            1 |          4 |            6975 |          0 |     0 | N            | O            | 1998-10-06 | 1998-08-25   | 1998-10-18    | COLLECT COD    | MAIL       | dinos sublat                               |
+------------+-----------+-----------+--------------+------------+-----------------+------------+-------+--------------+--------------+------------+--------------+---------------+----------------+------------+--------------------------------------------+
10 rows in set (0.344 sec)


MySQL [ssb_30g]> create index I_L_SHIPDATE_COMMITDATE on lineitem(l_shipdate, l_commitdate) local;
Query OK, 0 rows affected (1 min 49.907 sec)

MySQL [ssb_30g]> select  * from lineitem order by l_shipdate,l_commitdate  limit 10;
+------------+-----------+-----------+--------------+------------+-----------------+------------+-------+--------------+--------------+------------+--------------+---------------+-------------------+------------+------------------------------------------+
| l_orderkey | l_partkey | l_suppkey | l_linenumber | l_quantity | l_extendedprice | l_discount | l_tax | l_returnflag | l_linestatus | l_shipdate | l_commitdate | l_receiptdate | l_shipinstruct    | l_shipmode | l_comment                                |
+------------+-----------+-----------+--------------+------------+-----------------+------------+-------+--------------+--------------+------------+--------------+---------------+-------------------+------------+------------------------------------------+
|   60137444 |    574812 |    274813 |            3 |         19 |           35849 |          0 |     0 | A            | F            | 1992-01-02 | 1992-01-31   | 1992-01-13    | COLLECT COD       | SHIP       | cial, express tithes i                   |
|  121365347 |   4101510 |    126550 |            2 |         38 |           57430 |          0 |     0 | R            | F            | 1992-01-02 | 1992-01-31   | 1992-01-09    | DELIVER IN PERSON | TRUCK      | wly ironic courts above t                |
|   15413986 |   5397809 |    297810 |            4 |         14 |           26692 |          0 |     0 | A            | F            | 1992-01-02 | 1992-01-31   | 1992-01-04    | COLLECT COD       | TRUCK      | structions. blithely pending asymptotes  |
|   26696039 |   2634716 |    159741 |            2 |         50 |           82529 |          0 |     0 | R            | F            | 1992-01-02 | 1992-01-31   | 1992-01-13    | DELIVER IN PERSON | REG AIR    | . furiously regul                        |
|   46646336 |    912361 |    162368 |            1 |         44 |           60426 |          0 |     0 | A            | F            | 1992-01-02 | 1992-01-31   | 1992-01-05    | NONE              | TRUCK      | heaves. furio                            |
|   10359200 |   3057020 |     57021 |            6 |         42 |           41029 |          0 |     0 | R            | F            | 1992-01-02 | 1992-01-31   | 1992-01-25    | COLLECT COD       | FOB        |  blithely regu                           |
|  167299810 |    331144 |    256148 |            2 |         15 |           17627 |          0 |     0 | A            | F            | 1992-01-02 | 1992-01-31   | 1992-01-03    | COLLECT COD       | FOB        | ernes; furiously expre                   |
|   82801346 |   3243073 |    243074 |            2 |         36 |           36573 |          0 |     0 | A            | F            | 1992-01-02 | 1992-01-31   | 1992-01-11    | TAKE BACK RETURN  | AIR        | unts. carefully even p                   |
|  129556928 |   1521254 |     21255 |            1 |         44 |           56108 |          0 |     0 | R            | F            | 1992-01-02 | 1992-01-31   | 1992-01-04    | TAKE BACK RETURN  | REG AIR    | across the regula                        |
|   31272229 |   3321328 |    246362 |            1 |          3 |            4047 |          0 |     0 | R            | F            | 1992-01-02 | 1992-02-01   | 1992-01-31    | COLLECT COD       | AIR        | l ideas alon                             |
+------------+-----------+-----------+--------------+------------+-----------------+------------+-------+--------------+--------------+------------+--------------+---------------+-------------------+------------+------------------------------------------+
10 rows in set (0.443 sec)


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




