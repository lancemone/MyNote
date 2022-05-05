# QuerySet

## Django QuerySet
QuerySet是Django提供的强大的数据库接口(API)。正是因为通过它，我们可以使用filter, exclude, get等方法进行数据库查询，而不需要使用原始的SQL语言与数据库进行交互。从数据库中查询出来的结果一般是一个集合，这个集合叫就做queryset


## 特性
### Django的QuerySet是惰性的
例如 `article_list = Article.objects.filter(title__contains="django")`,在article_list的时候，Django的数据接口QuerySet并没有对数据库进行任何查询。只有当需要对article_list做进一步运算时（比如打印出查询结果，判断是否存在，统计结果长度)，Django才会真正执行对数据库的查询。这个过程被称为queryset的执行(evaluation)。Django这样设计的本意是尽量减少对数据库的无效操作

### Django的QuerySet自带缓存(Cache)
当遍历article_list时，所有匹配的记录会从数据库获取。这些结果会载入内存并保存在queryset内置的cache中。这样如果再次遍历或读取这个article_list时，Django就不需要重复查询了，这样也可以减少对数据库的查询。  

* exists方法  
但有时我们只希望了解查询的结果是否存在，而不需要使用整个数据集，这时整个queryset的缓存变成了一件坏事情。可以用article_list.exists()方法，exists只会检查查询结果是否存在，返回True或False，而不会缓存article_list。
    > 判断查询结果是否存在到底用if还是exists取决于你是否希望缓存查询数据集复用，如果是用if，反之用exists

* count方法  
统计查询结果数量优选count方法,虽然len()与count()均能统计查询结果的数量。一般来说count更快，因为它是从数据库层面直接获取查询结果的数量，而不是返回整个数据集，而len会导致queryset的执行，需要将整个queryset载入内存后才能统计其长度。但如果数据集queryset已经在缓存里了，使用len更快，不需要再执行查询

* values/value_list方法  
当查询到的queryset的非常大时，会大量占用内存(缓存)。我们可以使用values和value_list方法按需提取数据,只提取部分字段的值。values和values_list分别以字典和元组形式返回查询结果，不再是queryset类型数据。

* update方法  
update方法更新数据库部分字段，如果需要对数据库中的某条已有数据或某些字段进行更新，更好的方式是用update，而不是save方法。update方法还会返回已更新条目的数量。

* bulk_create/bulk_update  
批量创建或更新数据使用bulk_create或bulk_update

* explain方法  
explain方法，可以统计一个查询所消耗的执行时间

