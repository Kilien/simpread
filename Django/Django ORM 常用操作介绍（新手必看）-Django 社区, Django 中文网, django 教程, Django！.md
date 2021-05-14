> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.django.cn](https://www.django.cn/article/show-15.html)

Django 开发过程中对表 (model) 的增删改查是最常用的功能之一，本文介绍笔者在使用 model 操作过程中遇到的一些操作。

假如我们的表结构是这样的

```
class User(models.Model):
    username = models.CharField(max_length=255, unique=True, verbose_name='用户名')
    is_active = models.BooleanField(default=False, verbose_name='激活状态')


```

那么我们修改用户名和状态可以使用如下两种方法：

方法一：

```
User.objects.filter(id=1).update(username='nick',is_active=True)


```

方法二：

```
_t = User.objects.get(id=1)
_t.username='nick'
_t.is_active=True
_t.save()


```

方法一适合更新一批数据，类似于 mysql 语句`update user set username='nick' where id = 1`

方法二适合更新一条数据，也只能更新一条数据，当只有一条数据更新时推荐使用此方法，另外此方法还有一个好处，我们接着往下看

我们通常会给表添加三个默认字段

*   自增 ID，这个 django 已经默认加了，就像上边的建表语句，虽然只写了 username 和 is_active 两个字段，但表建好后也会有一个默认的自增 id 字段
    
*   创建时间，用来标识这条记录的创建时间，具有`auto_now_add`属性，创建记录时会自动填充当前时间到此字段
    
*   修改时间，用来标识这条记录最后一次的修改时间，具有`auto_now`属性，当记录发生变化时填充当前时间到此字段
    

就像下边这样的表结构

```
class User(models.Model):
    create_time = models.DateTimeField(auto_now_add=True, verbose_name='创建时间')
    update_time = models.DateTimeField(auto_now=True, verbose_name='更新时间')
    username = models.CharField(max_length=255, unique=True, verbose_name='用户名')
    is_active = models.BooleanField(default=False, verbose_name='激活状态')


```

**当表有字段具有`auto_now`属性且你希望他能自动更新时，必须使用上边方法二的更新，不然 auto_now 字段不会更新**，也就是：

```
_t = User.objects.get(id=1)
_t.username='nick'
_t.is_active=True
_t.save()


```

目前主流的 web 开放方式都讲究前后端分离，分离之后前后端交互的数据格式大都用通用的 json 型，那么如何用最少的代码方便的更新 json 格式数据到数据库呢？同样可以使用如下两种方法：

方法一：

```
data = {'username':'nick','is_active':'0'}
User.objects.filter(id=1).update(**data)


```

*   同样这种方法不能自动更新具有`auto_now`属性字段的值
    
*   通常我们再变量前加一个星号 (*) 表示这个变量是元组 / 列表，加两个星号表示这个参数是字典
    

方法二：

```
data = {'username':'nick','is_active':'0'}
_t = User.objects.get(id=1)
_t.__dict__.update(**data)
_t.save()


```

*   方法二和方法一同样无法自动更新`auto_now`字段的值
    
*   注意这里使用到了一个`**dict**`方法
    

方法三：

```
_t = User.objects.get(id=1)
_t.role=Role.objects.get(id=3)
_t.save()


```

假如我们的表中有 Foreignkey 外键时，该如何更新呢？

```
class User(models.Model):
    create_time = models.DateTimeField(auto_now_add=True, verbose_name='创建时间')
    update_time = models.DateTimeField(auto_now=True, verbose_name='更新时间')
    username = models.CharField(max_length=255, unique=True, verbose_name='用户名')
    is_active = models.BooleanField(default=False, verbose_name='激活状态')
    role = models.ForeignKey(Role, on_delete=models.CASCADE, null=True, verbose_name='角色')


```

方法一：

```
User.objects.filter(id=1).update(role=2)


```

*   最简单的方法，直接让给 role 字段设置为一个 id 即可
    
*   当然也可以用 dict 作为参数更新：
    

```
User.objects.filter(id=1).update(**{'username':'nick','role':3})


```

方法二：

```
_role = Role.objects.get(id=2)
User.objects.filter(id=1).update(role=_role)


```

*   也可以赋值一个实例给 role
    
*   当然也可以用 dict 作为参数更新：
    

```
_role = Role.objects.get(id=1)
User.objects.filter(id=1).update(**{'username':'nick','role':_role})


```

方法三：

```
_t = User.objects.get(id=1)
_t.role=Role.objects.get(id=3)
_t.save()


```

*   注意：**这里的 role 必须赋值为一个对象，不能写 id**，不然会报错`"User.role" must be a "Role" instance`
    
*   当使用 dict 作为参数更新时又有一点不同，如下代码：
    

```
_t = User.objects.get(id=1)
_t.__dict__.update(**{'username':'nick','role_id':2})
_t.save()


```

*   **Foreignkey 外键必须加上 `_id`**，例如：{'role_id':3}
    
*   role_id 后边必须跟一个 id（int 或 str 类型都可），不能跟 role 实例
    

假如我们的表中有 ManyToManyField 字段时更新又有什么影响呢？

```
class User(models.Model):
    create_time = models.DateTimeField(auto_now_add=True, verbose_name='创建时间')
    update_time = models.DateTimeField(auto_now=True, verbose_name='更新时间')
    username = models.CharField(max_length=255, unique=True, verbose_name='用户名')
    is_active = models.BooleanField(default=False, verbose_name='激活状态')
    role = models.ForeignKey(Role, on_delete=models.CASCADE, null=True, verbose_name='角色')
    groups = models.ManyToManyField(Group, null=True, verbose_name='组')


```

m2m 更新：m2m 字段没有直接更新的方法，只能通过清空再添加的方法更新了

```
_t = User.objects.get(id=1)
_t.groups.clear()
_t.groups.add(*[1,3,5])
_t.save()


```

*   `add()`：m2m 字段添加一个值，当有多个值的时候可用列表，参照上边例子
    

*   _t.groups.add(2)
    
*   _t.groups.add(Group.objects.get(id=2))
    

*   `remove()`：m2m 字段移除一个值，，当有多个值的时候可用列表，参照上边例子
    

*   _t.groups.remove(2)
    
*   _t.groups.remove(Group.objects.get(id=2))
    

*   `clear()`：清空 m2m 字段的值
    

Q 对象可以对关键字参数进行封装，从而更好的应用多个查询，可以组合 &(and)、|(or)、~(not) 操作符。

例如下边的语句

```
from django.db.models import Q

User.objects.filter(
    Q(role__startswith='sre_'),
    Q(name='公众号') | Q(name='运维咖啡吧')
)

```

转换成 SQL 语句如下：

```
select * from User where role like 'sre_%' and (name='公众号' or name='运维咖啡吧')


```

通常更多的时候我们用 Q 来做搜索逻辑，比如前台搜索框输入一个字符，后台去数据库中检索标题或内容中是否包含

```
_s = request.GET.get('search')

_t = Blog.objects.all()
if _s:
    _t = _t.filter(
        Q(title__icontains=_s) |
        Q(content__icontains=_s)
    )

return _t

```

*   表结构：
    

```
class Role(models.Model):
    name = models.CharField(max_length=16, unique=True)

class User(models.Model):
    username = models.EmailField(max_length=255, unique=True)
    role = models.ForeignKey(Role, on_delete=models.CASCADE)

```

*   正向查询:
    

*   反向查询：
    

*   另一种反向查询的方法：
    

```
_t = Role.objects.get(name='Role03')

# 这种方法比上一种_set的方法查询速度要快
User.objects.filter(role=_t)

```

*   第三种反向查询的方法：
    

如果外键字段有`related_name`属性，例如 models 如下：

```
class User(models.Model):
    username = models.EmailField(max_length=255, unique=True)
    role = models.ForeignKey(Role, on_delete=models.CASCADE,related_name='roleUsers')


```

那么可以直接用`related_name`属性取到某角色的所有用户

```
_t = Role.objects.get(name = 'Role03')
_t.roleUsers.all()


```

*   表结构：
    

```
class Group(models.Model):
    name = models.CharField(max_length=16, unique=True)

class User(models.Model):
    username = models.CharField(max_length=255, unique=True)
    groups = models.ManyToManyField(Group, related_name='groupUsers')

```

*   正向查询:
    

*   反向查询：
    

同样 M2M 字段如果有`related_name`属性，那么可以直接用下边的方式反查

```
_t = Group.objects.get(name = 'groupC')
_t.groupUsers.all()


```

正常如果我们要去数据库里搜索某一条数据时，通常使用下边的方法：

```
_t = User.objects.get(id=734)


```

但当`id=724`的数据不存在时，程序将会抛出一个错误

```
abcer.models.DoesNotExist: User matching query does not exist.


```

为了程序兼容和异常判断，我们可以使用下边两种方式:

*   方式一：`get`改为`filter`
    

```
_t = User.objects.filter(id=724)
# 取出_t之后再去判断_t是否存在


```

*   方式二：使用`get_object_or_404`
    

```
from django.shortcuts import get_object_or_404

_t = get_object_or_404(User, id=724)


```

实现方法类似于下边这样：

```
from django.http import Http404

try:
    _t = User.objects.get(id=724)
except User.DoesNotExist:
    raise Http404

```

顾名思义，查找一个对象如果不存在则创建，如下：

```
object, created = User.objects.get_or_create(username='运维咖啡吧')


```

返回一个由 object 和 created 组成的元组，其中 object 就是一个查询到的或者是被创建的对象，created 是一个表示是否创建了新对象的布尔值

实现方式类似于下边这样：

```
try:
    object = User.objects.get(username='运维咖啡吧')

    created = False
exception User.DoesNoExist:
    object = User(username='运维咖啡吧')
    object.save()

    created = True

returen object, created

```

Django 中能用 ORM 的就用它 ORM 吧，不建议执行原生 SQL，可能会有一些安全问题，如果实在是 SQL 太复杂 ORM 实现不了，那就看看下边执行原生 SQL 的方法，跟直接使用 pymysql 基本一致了

```
from django.db import connection

with connection.cursor() as cursor:
    cursor.execute('select * from accounts_User')
    row = cursor.fetchall()

return row

```

注意这里表名字要用 **app 名 + 下划线 + model 名**的方式

本文内容来自群友的公众号：**运维咖啡吧**  

![](https://www.django.cn/media/upimg/222_20180816170857_148.jpg)

**码字不容易，转载请加上本文链接和注明出处。如果上面的内容帮到你了，可以打赏作者喝杯茶。**

![](https://www.django.cn/media/upimg/117_20200606023808_903.jpg)