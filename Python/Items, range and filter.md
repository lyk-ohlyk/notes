
最近项目准备Python2升3了，群里有了一些关于Python的讨论，有同事提出：为什么dict的items，以及range(n)的bool结果都是和里面有没有值是对应的，而 filter 函数返回的迭代器却是和内容无关（因为迭代器的bool永远是True）。不解之处在于，为什么都是迭代器，会有不一致的表现？群里讨论了一会，我说看返回类型的CPython设计，但并没被认可，一人说可能因为filter迭代器不能预执行，可能会影响实际结果，这个被认可了。我认为只要是迭代器，就不应该有所谓的“预生成”或者“预判断”的机制，因为显然是有后效性的。

这里的问题核心在于，items 和 range 的返回结果，真的是迭代器吗？其实不然
```python
>>> {}.items()
dict_items([])

>>> range(3)
range(0, 3)
```
可以看到，他们各自返回的都是特殊的类型，当然我们可以直接去看其CPython实现来看bool的作用效果，但核心还是在于，他们并不是迭代器，而是“访问器”。
```python
>>> next({}.items())

Traceback (most recent call last):

  File "<stdin>", line 1, in <module>

TypeError: 'dict_items' object is not an iterator

>>> next(range(3))

Traceback (most recent call last):

  File "<stdin>", line 1, in <module>

TypeError: 'range' object is not an iterator
```
可以看到根本就没有实现 `__next__` 函数，这样和我的理解就是一致的：迭代器不应该对其实现有所感知，不应该默认自动执行 `__next__` 。

至于 dict_items 和 range 这两个类，为什么能在不创建`list`也记录数据大小，是因为这两个类本身就记录了数据。

当然，访问的时候，dict_items和range都是用迭代器访问的，脚本上是：
```python
{}.items().__iter__()  # dict_itemiterator -> PyDictIterItem_Type
range(3).__iter__()  # range_iterator -> PyRangeIter_Type
```

所以回到最初的问题，可以总结下面几点：
- Python用迭代器来作为数据访问的工具，但并不意味着使用迭代器的类本身是迭代器；
- 迭代器就应该做到在访问前对数据本身无感知，否则会有正确性的问题；
- Python中的每个内置类应该都有实现对应的迭代器类型，比如`dict`用到的3种迭代器：
```python
>>> {}.keys().__iter__()
<dict_keyiterator object at 0x1046eedb0>

>>> {}.items().__iter__()
<dict_itemiterator object at 0x104753180>

>>> {}.values().__iter__()
<dict_valueiterator object at 0x1046eedb0>
```

