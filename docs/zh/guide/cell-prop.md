# 单元格基本属性

::: warning
所有的单元格属性中的文本，以等号开始的就是表达式，其他是纯文本
:::

## 值表达式

- **值表达式** 是计算报表每一个具体单元格的实际值用到的公式。它是报表计算的核心表达式。所有的取数表达式都是在值表达式中定义。
  
## 显示值表达式
 显示值表达式是为了显示让用户更友好的阅读数据所用到的公式。**缺省公式为`=@value`。@value表示的意思是当前值表达式的值，*不能在值表达式中使用@value。*在这里可以重新组织该单元格的显示数据。比如格式化数据、显示值单元格中所取代码的代表的实际名称等。
   + 如果单元格的数据是数字类型，可以格式化数字，公式是：`=formatNumber（@value）`缺省格式为：#######.##，可以在第二个参数指定格式。如：####.0000。
   + 如果单元格是某个group函数取出的code代码，显示值表达式最好是另建一个数据集，比如叫：仓库，sql为：`select 名称，id from 仓库表`，然后在显示值表达式中写:`=仓库.select1(仓库.名称,仓库.id==@value)`，你的其他sql中都不用取名称，这样做的好处，一方面能有效减少数据量，而且更容易维护；另一个好处，在需要group的数据集上不用关联取名称，一定程度的提高sql的执行效率。
   + 如果该单元格的显示值需要从数据库查询，可以类似这样写：`=query(数据库连接变量，”select xxx from table where yyy=’+@value+”’”)`。这种方式取数相对于先定义数据集，然后用ds.select1的方式取数，但这样会多次调用数据库查询，效率很低。不推荐这种方式，会让报表生成速度大幅下降。
   + 如果单元格的值为零，显示为空白，公式为：=iif(@value==0,’’,@value)
   + 如果单元格的值为万元为单位，公式为：`= @value/10000`,和上面的为零显示空白结合，公式为：`=iif(@value==0,’’,@value/10000)`
::: warning  
  不要在值表达式以为的属性中使用 分组、归并等函数。
:::
## 字体颜色和背景颜色
- 针对字体颜色和背景颜色，我们也可以使用表达式动态计算，具体的颜色选取，参考设计器的下拉框。
- **类似excel中的条件格式设置。** 我们可以选中设计器中要启用条件格式的单元格，选菜单上的条件格式，选取合适的方式，就可以实现类似excel中的条件格式设置。我们也可以设置通用模式，参看 [这里](../../zh/conf/base.md#前端隔行变色和条件颜色的配置)

## 报表的钻取、跳转
- 这个功能主要是通过单元格的链接属性实现的。在设计器上，点击该属性后的图标，进入表达式编辑页面，点：`生成url连接串`，就可以自动生成缺省的url连接串，缺省会将所有的非内部属性组织到该url里面，其中有reportName=param.ReportName，表示的是当前的报表名，如果要跳转到其他报表，把这里的报表名称换成指定的报表名即可。如果要钻取，通常是报表名不变，参数改变，而且这通常发生在分组单元格上。比

## 排序
- 如某公司的省市县三级机构，报表开始是显示的省公司的报表，其明细是各地市的数据，可以设置报表由省公司报表向地市公司报表的钻取。

- **只要是只有一个行扩展（列扩展数不限）的报表，都已经内置了点报表表头排序。多个行扩展的报表，点击表头排序的功能是无意义的。**

<img :src="$withBase('//img/微信图片_20211227111416.png')" alt="foo">

## 扩展方向、左顶格、上顶格

::: tip
(这里可以作为提高对报表运行认知的时候查看。初学可以不用理会)
:::

新建的报表由行列整齐的格子组成，这些格子我们称为单元格，通过所有单元格的值和边框排列在一起，组成了报表。

每一个单元格都有扩展方向、左顶格、上顶格。缺省的扩展方向是空（等同于不扩展）。如果扩展方向为向下，那么报表生成时，将会按当前单元格的值（通常是List列表）向下展开。向右扩展，同理。

每个单元格都有左顶格和上顶格。单元格的左顶格属性寻找规则：同一行上，向左找到第一个有向下扩展属性的单元格，就是自己的左顶格，如果手动指定以指定为准。如果没有找打具有向下扩展属性的单元格，缺省就是隐含的rootCell: `0 .

每个可扩展单元格内部都具有属性：拥有的数据子集。
每个单元格都拥有内部属性：当前属于哪个数据子集。所有单元格的计算，都依赖于此展开。这个属性是根据自己左顶格和上顶格拥有的数据子集的交集计算得到。rootCell拥有的是整个数据集.
