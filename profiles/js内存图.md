### 图示讲解js内存
* Q1:

```
var a = 1;
var b = a;
b = 2;
a = ? 
```

* Q2:

```
var a = { name: 'a' };
var b = a;
b.name = 'b';
a.name = ?
```

* Q3:

```
var a = { name: 'a' };
var b = a;
b = { 'name': 'b' };
a.name = ?
```

* Q4:

```
var a = { name: 'a' };
var b = a;
b = null;
a = ?
```