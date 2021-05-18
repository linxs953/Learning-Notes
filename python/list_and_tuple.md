## 列表

```python
typedef struct {
    PyObject_VAR_HEAD
    /* Vector of pointers to list elements.  list[0] is ob_item[0], etc. */
    PyObject **ob_item;

    /* ob_item contains space for 'allocated' elements.  The number
     * currently in use is ob_size.
     * Invariants:
     *     0 <= ob_size <= allocated
     *     len(list) == ob_size
     *     ob_item == NULL implies ob_size == allocated == 0
     * list.sort() temporarily sets allocated to -1 to detect mutations.
     *
     * Items must normally not be NULL, except during construction when
     * the list is not yet visible outside the function that builds it.
     */
    Py_ssize_t allocated;
} PyListObject;
```


- ob_item指向一个存储元素的指针数组，len(ob_item)==ob_size
- allocated记录存储的已分配的空间的大小
- 当前空间已满，即allocated==len(list)，根据特定的扩容策略申请一块新的内存，然后把原有的数据拷贝过去

## 元组


```python
typedef struct {
    PyObject_VAR_HEAD
    /* ob_item contains space for 'ob_size' elements.
       Items must normally not be NULL, except during construction when
       the tuple is not yet visible outside the function that builds it. */
    PyObject *ob_item[1];
} PyTupleObject;
```

- ob_item指向一个数组，数组内容不可变
- 当数组个数少于20个时，通过一个free_list的东西缓存起来，后面有创建同样数据的tuple时，python可以从缓存中载入



## hash


> 旧hash

- 通过一个数组entries来存储数据
- 当空闲的空间少于1 / 3时，为了避免hash冲突，会申请更大的空间来存放数据，这样 导致空闲的空间越来越多，浪费空间

> 新hash

- 增加一个indics数组来存放数据的索引，如果空间只是分配未使用，则显示None
- 提升了空间利用率