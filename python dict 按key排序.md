OrderedDict是collections中的一个包，能够记录字典元素插入的顺序，常常和排序函数一起使用来生成一个排序的字典。

比如，比如一个无序的字典
from  collections import OrderedDict

d = {‘banana’:3,’apple’:4,’pear’:1,’orange’:2}

通过排序来生成一个有序的字典，有以下几种方式

collections.OrderedDict(sorted(d.items(),key = lambda t:t[0]))

或者

collections.OrderedDict(sorted(d.items(),key = lambda t:t[1]))

或者

collections.OrderedDict(sorted(d.items(),key = lambda t:len(t[0])))

for k,v in d.items():
    print '%s:%s' % (k, d[k])