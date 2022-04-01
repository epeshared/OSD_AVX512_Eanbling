# 1. Code location

https://github.com/oceanbase/oceanbase/compare/master...xtangxtang:master

## 1.1 Code calling graph
**ob_avx512_sort.cpp**::sort_loop --> **ObSortRow**::get_sort_key --> **ObObj**::make_sort_key --> **ObCharset**::sortkey_v2 --> **ob_ctype.h**::strnxfrm_v2
