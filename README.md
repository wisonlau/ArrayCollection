1、简介
Collection 类为处理数组数据提供流式、方便的封装。例如，查看下面的代码，我们使用辅助函数 collect 创建一个新的集合实例，为每一个元素运行 strtoupper 函数，然后移除所有空元素：

$collection = collect(['taylor', 'abigail', null])->map(function ($name) {
    return strtoupper($name);
})->reject(function ($name) {
    return empty($name);
});
正如你所看到的，Collection 类允许你使用方法链对底层数组执行匹配和移除操作，通常，每个 Collection 方法都会返回一个新的 Collection 实例。

2、创建集合
正如上面所提到的，辅助函数 collect 为给定数组返回一个新的 Collection 实例，所以，创建集合很简单：

$collection = collect([1, 2, 3]);
注：默认情况下，Eloquent 查询的结果总是返回 Collection 实例。
3、集合方法
本文档接下来的部分将会介绍 Collection 类上每一个有效的方法，所有这些方法都可以以方法链的方式流式操作底层数组。此外，几乎每个方法返回一个新的 Collection 实例，从而允许你在必要的时候保持原来的集合备份。

4、方法列表
all()

all 方法简单返回集合表示的底层数组：

collect([1, 2, 3])->all();
// [1, 2, 3]
avg()

avg 方法返回所有集合项的平均值：

collect([1, 2, 3, 4, 5])->avg();
// 3
如果集合包含嵌套的数组或对象，需要指定要使用的键以判定计算哪些值的平均值：

$collection = collect([
    ['name' => 'JavaScript: The Good Parts', 'pages' => 176],
    ['name' => 'JavaScript: The Definitive Guide', 'pages' => 1096],、
]);

$collection->avg('pages');
// 636
chunk()

chunk 方法将一个集合分割成多个小尺寸的小集合：

$collection = collect([1, 2, 3, 4, 5, 6, 7]);
$chunks = $collection->chunk(4);
$chunks->toArray();
// [[1, 2, 3, 4], [5, 6, 7]]
当处理栅栏系统如 Bootstrap 时该方法在视图中尤其有用，建设你有一个想要显示在栅栏中的 Eloquent 模型集合：

@foreach ($products->chunk(3) as $chunk)
    <div class="row">
        @foreach ($chunk as $product)
            <div class="col-xs-4">{{ $product->name }}</div>
        @endforeach
    </div>
@endforeach
collapse()

collapse 方法将一个多维数组集合收缩成一个一维数组：

$collection = collect([[1, 2, 3], [4, 5, 6], [7, 8, 9]]);
$collapsed = $collection->collapse();
$collapsed->all();
// [1, 2, 3, 4, 5, 6, 7, 8, 9]
combine()

combine 方法可以将一个集合的键和另一个数组或集合的值连接起来：

$collection = collect(['name', 'age']);

$combined = $collection->combine(['George', 29]);

$combined->all();
// ['name' => 'George', 'age' => 29]
contains()

contains 方法判断集合是否包含一个给定项：

$collection = collect(['name' => 'Desk', 'price' => 100]);

$collection->contains('Desk');
// true
$collection->contains('New York');
// false
你还可以传递一个键值对到 contains 方法，这将会判断给定键值对是否存在于集合中：

$collection = collect([
    ['product' => 'Desk', 'price' => 200],
    ['product' => 'Chair', 'price' => 100],
]);

$collection->contains('product', 'Bookcase');
// false
最后，你还可以传递一个回调到 contains 方法来执行自己的真实测试：

$collection = collect([1, 2, 3, 4, 5]);
$collection->contains(function ($key, $value) {
    return $value > 5;
});
// false
count()

count 方法返回集合中所有项的总数：

$collection = collect([1, 2, 3, 4]);
$collection->count();
// 4
diff()

diff 方法将集合和另一个集合或原生PHP数组以基于值的方式作比较，这个方法会返回存在于原来集合而不存在于给定集合的值：

$collection = collect([1, 2, 3, 4, 5]);
$diff = $collection->diff([2, 4, 6, 8]);
$diff->all();
// [1, 3, 5]
diffKeys()

diffKeys 方法会基于犍对集合和另一个集合或原生PHP数组进行比较。该方法会返回存在于原来集合而不存在于给定集合的键值对：

$collection = collect([
    'one' => 10,
    'two' => 20,
    'three' => 30,
    'four' => 40,
    'five' => 50,
]);

$diff = $collection->diffKeys([
    'two' => 2,
    'four' => 4,
    'six' => 6,
    'eight' => 8,
]);

$diff->all();
// ['one' => 10, 'three' => 30, 'five' => 50]
each()

each 方法迭代集合中的数据项并传递每个数据项到给定回调：

$collection = $collection->each(function ($item, $key) {
    //
});
如果你想要终止对数据项的迭代，可以从回调返回 false：

$collection = $collection->each(function ($item, $key) {
    if (/* some condition */) {
        return false;
    }
});
every()

every 方法可以用于验证集合的所有元素能够通过给定的真理测试：

collect([1, 2, 3, 4])->every(function ($value, $key) {
    return $value > 2;
});

// false
except()

except 方法返回集合中除了指定键的所有集合项：

$collection = collect(['product_id' => 1, 'name' => 'Desk', 'price' => 100, 'discount' => false]);

$filtered = $collection->except(['price', 'discount']);

$filtered->all();
// ['product_id' => 1, 'name' => 'Desk']
与 except 相对的是 only 方法。

filter()

filter 方法通过给定回调过滤集合，只有通过给定真理测试的数据项才会保留下来：

$collection = collect([1, 2, 3, 4]);

$filtered = $collection->filter(function ($value, $key) {
    return $value > 2;
});

$filtered->all();
// [3, 4]
和 filter 相对的方法是 reject。

first()

first 方法返回通过真理测试集合的第一个元素：

collect([1, 2, 3, 4])->first(function ($value, $key) {
    return $value > 2;
});
// 3
你还可以调用不带参数的 first 方法来获取集合的第一个元素，如果集合是空的，返回null：

collect([1, 2, 3, 4])->first();
// 1
flatMap()

flatMap 方法会迭代集合并传递每个值到给定回调，该回调可以自由编辑数据项并将其返回，最后形成一个经过编辑的新集合。然后，这个数组在层级维度被扁平化：

$collection = collect([
    ['name' => 'Sally'],
    ['school' => 'Arkansas'],
    ['age' => 28]
]);

$flattened = $collection->flatMap(function ($values) {
    return array_map('strtoupper', $values);
});

$flattened->all();

// ['name' => 'SALLY', 'school' => 'ARKANSAS', 'age' => '28'];
flatten()

flatten 方法将多维度的集合变成一维的：

$collection = collect(['name' => 'taylor', 'languages' => ['php', 'javascript']]);

$flattened = $collection->flatten();

$flattened->all();

// ['taylor', 'php', 'javascript'];
还可以选择性传入深度参数：

$collection = collect([
    'Apple' => [
        ['name' => 'iPhone 6S', 'brand' => 'Apple'],
    ],
    'Samsung' => [
        ['name' => 'Galaxy S7', 'brand' => 'Samsung']
    ],
]);

$products = $collection->flatten(1);

$products->values()->all();

/*
[
    ['name' => 'iPhone 6S', 'brand' => 'Apple'],
    ['name' => 'Galaxy S7', 'brand' => 'Samsung'],
]
*/
在本例中，调用不提供深度的 flatten 方法也会对嵌套数组进行扁平化处理，返回结果是 ['iPhone 6S', 'Apple', 'Galaxy S7', 'Samsung']。提供深度允许你严格设置被扁平化的数组层级。

flip()

flip 方法将集合的键值做交换：

$collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

$flipped = $collection->flip();

$flipped->all();

// ['taylor' => 'name', 'laravel' => 'framework']
forget()

forget 方法通过键从集合中移除数据项：

$collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

$collection->forget('name');

$collection->all();

// [framework' => 'laravel']
注：不同于大多数其他的集合方法，forget 不返回新的修改过的集合；它只修改所调用的集合。
forPage()

forPage 方法返回新的包含给定页数数据项的集合。该方法接收页码数作为第一个参数，每页显示数据项数作为第二个参数：

$collection = collect([1, 2, 3, 4, 5, 6, 7, 8, 9]);

$chunk = $collection->forPage(2, 3);

$chunk->all();

// [4, 5, 6]
get()

get 方法返回给定键的数据项，如果对应键不存在，返回null：

$collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

$value = $collection->get('name');

// taylor
你可以选择传递默认值作为第二个参数：

$collection = collect(['name' => 'taylor', 'framework' => 'laravel']);

$value = $collection->get('foo', 'default-value');

// default-value
你甚至可以传递回调作为默认值，如果给定键不存在的话回调的结果将会返回：

$collection->get('email', function () {
    return 'default-value';
});
// default-value
groupBy()

groupBy 方法通过给定键分组集合数据项：

$collection = collect([
    ['account_id' => 'account-x10', 'product' => 'Chair'],
    ['account_id' => 'account-x10', 'product' => 'Bookcase'],
    ['account_id' => 'account-x11', 'product' => 'Desk'],
]);

$grouped = $collection->groupBy('account_id');

$grouped->toArray();

/*
[
    'account-x10' => [
        ['account_id' => 'account-x10', 'product' => 'Chair'],
        ['account_id' => 'account-x10', 'product' => 'Bookcase'],
    ],
    'account-x11' => [
        ['account_id' => 'account-x11', 'product' => 'Desk'],
    ],
]
*/
除了传递字符串key，还可以传递一个回调，回调应该返回分组后的值：

$grouped = $collection->groupBy(function ($item, $key) {
    return substr($item['account_id'], -3);
});

$grouped->toArray();

/*
[
    'x10' => [
        ['account_id' => 'account-x10', 'product' => 'Chair'],
        ['account_id' => 'account-x10', 'product' => 'Bookcase'],
    ],
    'x11' => [
        ['account_id' => 'account-x11', 'product' => 'Desk'],
    ],
]
*/
has()

has 方法判断给定键是否在集合中存在：

$collection = collect(['account_id' => 1, 'product' => 'Desk']);

$collection->has('email');

// false
implode()

implode 方法连接集合中的数据项。其参数取决于集合中数据项的类型。如果集合包含数组或对象，应该传递你想要连接的属性键，以及你想要放在值之间的 “粘合”字符串：

$collection = collect([
    ['account_id' => 1, 'product' => 'Desk'],
    ['account_id' => 2, 'product' => 'Chair'],
]);

$collection->implode('product', ', ');

// Desk, Chair
如果集合包含简单的字符串或数值，只需要传递“粘合”字符串作为唯一参数到该方法：

collect([1, 2, 3, 4, 5])->implode('-');

// '1-2-3-4-5'
intersect()

intersect 方法返回两个集合的交集，结果集合将保留原来集合的键：

$collection = collect(['Desk', 'Sofa', 'Chair']);

$intersect = $collection->intersect(['Desk', 'Chair', 'Bookcase']);

$intersect->all();

// [0 => 'Desk', 2 => 'Chair']
isEmpty()

如果集合为空的话 isEmpty 方法返回 true；否则返回 false：

collect([])->isEmpty();
// true
keyBy()

keyBy 方法将指定键的值作为集合的键，如果多个数据项拥有同一个键，只有最后一个会出现在新集合里面：

$collection = collect([
    ['product_id' => 'prod-100', 'name' => 'desk'],
    ['product_id' => 'prod-200', 'name' => 'chair'],
]);

$keyed = $collection->keyBy('product_id');

$keyed->all();

/*
[
    'prod-100' => ['product_id' => 'prod-100', 'name' => 'Desk'],
    'prod-200' => ['product_id' => 'prod-200', 'name' => 'Chair'],
]
*/
你还可以传递自己的回调到该方法，该回调将会返回经过处理的键的值作为新的集合键：

$keyed = $collection->keyBy(function ($item) {
    return strtoupper($item['product_id']);
});

$keyed->all();

/*
    [
        'PROD-100' => ['product_id' => 'prod-100', 'name' => 'Desk'],
        'PROD-200' => ['product_id' => 'prod-200', 'name' => 'Chair'],
    ]
*/
keys()

keys 方法返回所有集合的键：

$collection = collect([
    'prod-100' => ['product_id' => 'prod-100', 'name' => 'Desk'],
    'prod-200' => ['product_id' => 'prod-200', 'name' => 'Chair'],
]);

$keys = $collection->keys();

$keys->all();

// ['prod-100', 'prod-200']
last()

last 方法返回通过真理测试的集合的最后一个元素：

collect([1, 2, 3, 4])->last(function ($value, $key) {
    return $value < 3;
});
// 2
还可以调用无参的 last 方法来获取集合的最后一个元素。如果集合为空。返回 null：

collect([1, 2, 3, 4])->last();
// 4
map()

map 方法遍历集合并传递每个值给给定回调。该回调可以修改数据项并返回，从而生成一个新的经过修改的集合：

$collection = collect([1, 2, 3, 4, 5]);

$multiplied = $collection->map(function ($item, $key) {
    return $item * 2;
});

$multiplied->all();

// [2, 4, 6, 8, 10]
注：和大多数集合方法一样，map 返回新的集合实例；它并不修改所调用的实例。如果你想要改变原来的集合，使用transform方法。
mapWithKeys()

mapWithKeys 方法对集合进行迭代并传递每个值到给定回调，该回调会返回包含键值对的关联数组：

$collection = collect([
    [
        'name' => 'John',
        'department' => 'Sales',
        'email' => 'john@example.com'
    ],
    [
        'name' => 'Jane',
        'department' => 'Marketing',
        'email' => 'jane@example.com'
    ]
]);

$keyed = $collection->mapWithKeys(function ($item) {
    return [$item['email'] => $item['name']];
});

$keyed->all();

/*
[
    'john@example.com' => 'John',
    'jane@example.com' => 'Jane',
]
*/
max()

max 方法返回集合中给定键的最大值：

$max = collect([['foo' => 10], ['foo' => 20]])->max('foo');

// 20

$max = collect([1, 2, 3, 4, 5])->max();

// 5
merge()

merge 方法合并给定数组到集合。该数组中的任何字符串键匹配集合中的字符串键的将会重写集合中的值：

$collection = collect(['product_id' => 1, 'name' => 'Desk']);

$merged = $collection->merge(['price' => 100, 'discount' => false]);

$merged->all();

// ['product_id' => 1, 'name' => 'Desk', 'price' => 100, 'discount' => false]
如果给定数组的键是数字，数组的值将会附加到集合后面：

$collection = collect(['Desk', 'Chair']);

$merged = $collection->merge(['Bookcase', 'Door']);

$merged->all();

// ['Desk', 'Chair', 'Bookcase', 'Door']
min()

min 方法返回集合中给定键的最小值：

$min = collect([['foo' => 10], ['foo' => 20]])->min('foo');

// 10

$min = collect([1, 2, 3, 4, 5])->min();

// 1
nth()

nth方法组合集合中第n-th个元素创建一个新的集合：

$collection = collect(['a', 'b', 'c', 'd', 'e', 'f']);

$collection->nth(4);

// ['a', 'e']
还可以传递一个offset（偏移位置）作为第二个参数：

$collection->nth(4, 1);

// ['b', 'f']
only()

only 方法返回集合中指定键的集合项：

$collection = collect(['product_id' => 1, 'name' => 'Desk', 'price' => 100, 'discount' => false]);

$filtered = $collection->only(['product_id', 'name']);

$filtered->all();

// ['product_id' => 1, 'name' => 'Desk']
与 only 方法相对的是 except 方法。

partition()

partition方法可以和PHP函数list一起使用，从而将通过真理测试和没通过的分割开来：

$collection = collect([1, 2, 3, 4, 5, 6]);

list($underThree, $aboveThree) = $collection->partition(function ($i) {
    return $i < 3;
});
pipe()

pipe 方法传递集合到给定回调并返回结果：

$collection = collect([1, 2, 3]);

$piped = $collection->pipe(function ($collection) {
    return $collection->sum();
});

// 6
pluck()

pluck 方法为给定键获取所有集合值：

$collection = collect([
    ['product_id' => 'prod-100', 'name' => 'Desk'],
    ['product_id' => 'prod-200', 'name' => 'Chair'],
]);

$plucked = $collection->pluck('name');

$plucked->all();

// ['Desk', 'Chair']
还可以指定你想要结果集合如何设置键：

$plucked = $collection->pluck('name', 'product_id');

$plucked->all();

// ['prod-100' => 'Desk', 'prod-200' => 'Chair']
pop()

pop 方法移除并返回集合中最后面的数据项：

$collection = collect([1, 2, 3, 4, 5]);

$collection->pop();

// 5

$collection->all();

// [1, 2, 3, 4]
prepend()

prepend 方法添加数据项到集合开头：

$collection = collect([1, 2, 3, 4, 5]);

$collection->prepend(0);

$collection->all();

// [0, 1, 2, 3, 4, 5]
你还可以传递第二个参数到该方法用于设置前置项的键：

$collection = collect(['one' => 1, 'two', => 2]);

$collection->prepend(0, 'zero');

$collection->all();

// ['zero' => 0, 'one' => 1, 'two', => 2]
pull()

pull 方法通过键从集合中移除并返回数据项：

$collection = collect(['product_id' => 'prod-100', 'name' => 'Desk']);

$collection->pull('name');

// 'Desk'

$collection->all();

// ['product_id' => 'prod-100']
push()

push 方法附加数据项到集合结尾：

$collection = collect([1, 2, 3, 4]);

$collection->push(5);

$collection->all();

// [1, 2, 3, 4, 5]
put()

put 方法在集合中设置给定键和值：

$collection = collect(['product_id' => 1, 'name' => 'Desk']);

$collection->put('price', 100);

$collection->all();

// ['product_id' => 1, 'name' => 'Desk', 'price' => 100]
random()

random 方法从集合中返回随机数据项：

$collection = collect([1, 2, 3, 4, 5]);

$collection->random();

// 4 - (retrieved randomly)
你可以传递一个整型数据到 random 函数来指定返回的数据数目，如果该整型数值大于1，将会返回一个集合：

$random = $collection->random(3);

$random->all();

// [2, 4, 5] - (retrieved randomly)
reduce()

reduce 方法用于减少集合到单个值，传递每个迭代结果到子迭代：

$collection = collect([1, 2, 3]);

$total = $collection->reduce(function ($carry, $item) {
    return $carry + $item;
});

// 6
在第一次迭代时 $carry 的值是null；不过，你可以通过传递第二个参数到 reduce 来指定其初始值：

$collection->reduce(function ($carry, $item) {
    return $carry + $item;
}, 4);

// 10
reject()

reject 方法使用给定回调过滤集合，该回调应该为所有它想要从结果集合中移除的数据项返回 true：

$collection = collect([1, 2, 3, 4]);
$filtered = $collection->reject(function ($value, $key) {
    return $value > 2;
});

$filtered->all();
// [1, 2]
和 reject 方法相对的方法是filter 方法。

reverse()

reverse 方法将集合数据项的顺序颠倒：

$collection = collect([1, 2, 3, 4, 5]);

$reversed = $collection->reverse();

$reversed->all();

// [5, 4, 3, 2, 1]
search()

search 方法为给定值查询集合，如果找到的话返回对应的键，如果没找到，则返回 false：

$collection = collect([2, 4, 6, 8]);

$collection->search(4);

// 1
上面的搜索使用的是松散比较，要使用严格比较，传递 true 作为第二个参数到该方法：

$collection->search('4', true);
// false
此外，你还可以传递自己的回调来搜索通过真理测试的第一个数据项：

$collection->search(function ($item, $key) {
    return $item > 5;
});
// 2
shift()

shift 方法从集合中移除并返回第一个数据项：

$collection = collect([1, 2, 3, 4, 5]);

$collection->shift();

// 1

$collection->all();

// [2, 3, 4, 5]
shuffle()

shuffle 方法随机打乱集合中的数据项：

$collection = collect([1, 2, 3, 4, 5]);

$shuffled = $collection->shuffle();

$shuffled->all();
// [3, 2, 5, 1, 4] // (随机生成)
slice()

slice 方法从给定索引开始返回集合的一个切片：

$collection = collect([1, 2, 3, 4, 5, 6, 7, 8, 9, 10]);

$slice = $collection->slice(4);

$slice->all();

// [5, 6, 7, 8, 9, 10]
如果你想要限制返回切片的尺寸，将尺寸值作为第二个参数传递到该方法：

$slice = $collection->slice(4, 2);

$slice->all();

// [5, 6]
返回的切片有新的、数字化索引的键，如果你想要保持原有的键，可以使用 values 方法对它们进行重新索引。

sort()

sort 方法对集合进行排序， 排序后的集合保持原来的数组键，在本例中我们使用 values 方法重置键为连续编号索引：

$collection = collect([5, 3, 1, 2, 4]);

$sorted = $collection->sort();

$sorted->values()->all();

// [1, 2, 3, 4, 5]
如果你需要更加高级的排序，你可以使用自己的算法传递一个回调给 sort 方法。参考 PHP 官方文档关于 usort 的说明，sort 方法底层正是调用了该方法。

注：要为嵌套集合和对象排序，查看 sortBy 和 sortByDesc 方法。
sortBy()

sortBy 方法通过给定键对集合进行排序， 排序后的集合保持原有数组索引，在本例中，使用 values 方法重置键为连续索引：

$collection = collect([
    ['name' => 'Desk', 'price' => 200],
    ['name' => 'Chair', 'price' => 100],
    ['name' => 'Bookcase', 'price' => 150],
]);

$sorted = $collection->sortBy('price');

$sorted->values()->all();

/*
[
    ['name' => 'Chair', 'price' => 100],
    ['name' => 'Bookcase', 'price' => 150],
    ['name' => 'Desk', 'price' => 200],
]
*/
你还可以传递自己的回调来判断如何排序集合的值：

$collection = collect([
    ['name' => 'Desk', 'colors' => ['Black', 'Mahogany']],
    ['name' => 'Chair', 'colors' => ['Black']],
    ['name' => 'Bookcase', 'colors' => ['Red', 'Beige', 'Brown']],
]);

$sorted = $collection->sortBy(function ($product, $key) {
    return count($product['colors']);
});

$sorted->values()->all();

/*
    [
        ['name' => 'Chair', 'colors' => ['Black']],
        ['name' => 'Desk', 'colors' => ['Black', 'Mahogany']],
        ['name' => 'Bookcase', 'colors' => ['Red', 'Beige', 'Brown']],
    ]
*/
sortByDesc()

该方法和 sortBy 用法相同，不同之处在于按照相反顺序进行排序。

splice()

splice 方法从给定位置开始移除并返回数据项切片：

$collection = collect([1, 2, 3, 4, 5]);

$chunk = $collection->splice(2);

$chunk->all();

// [3, 4, 5]

$collection->all();

// [1, 2]
你可以传递参数来限制返回组块的大小：

$collection = collect([1, 2, 3, 4, 5]);

$chunk = $collection->splice(2, 1);

$chunk->all();

// [3]

$collection->all();

// [1, 2, 4, 5]
此外，你可以传递第三个包含新的数据项的参数来替代从集合中移除的数据项：

$collection = collect([1, 2, 3, 4, 5]);

$chunk = $collection->splice(2, 1, [10, 11]);

$chunk->all();

// [3]

$collection->all();

// [1, 2, 10, 11, 4, 5]
split()

split 方法通过给定数值对集合进行分组：

$collection = collect([1, 2, 3, 4, 5]);

$groups = $collection->split(3);

$groups->toArray();

// [[1, 2], [3, 4], [5]]
sum()

sum 方法返回集合中所有数据项的和：

collect([1, 2, 3, 4, 5])->sum();
// 15
如果集合包含嵌套数组或对象，应该传递一个键用于判断对哪些值进行求和运算：

$collection = collect([
    ['name' => 'JavaScript: The Good Parts', 'pages' => 176],
    ['name' => 'JavaScript: The Definitive Guide', 'pages' => 1096],
]);

$collection->sum('pages');

// 1272
此外，你还可以传递自己的回调来判断对哪些值进行求和：

$collection = collect([
    ['name' => 'Chair', 'colors' => ['Black']],
    ['name' => 'Desk', 'colors' => ['Black', 'Mahogany']],
    ['name' => 'Bookcase', 'colors' => ['Red', 'Beige', 'Brown']],
]);

$collection->sum(function ($product) {
    return count($product['colors']);
});
// 6
take()

take 方法使用指定数目的数据项返回一个新的集合：

$collection = collect([0, 1, 2, 3, 4, 5]);

$chunk = $collection->take(3);

$chunk->all();

// [0, 1, 2]
你还可以传递负数的方式从集合末尾开始获取指定数目的数据项：

$collection = collect([0, 1, 2, 3, 4, 5]);

$chunk = $collection->take(-2);

$chunk->all();

// [4, 5]
toArray()

toArray 方法将集合转化为一个原生的 PHP 数组。如果集合的值是 Eloquent 模型，该模型也会被转化为数组：

$collection = collect(['name' => 'Desk', 'price' => 200]);

$collection->toArray();

/*
    [
        ['name' => 'Desk', 'price' => 200],
    ]
*/
注：toArray 还将所有嵌套对象转化为数组。如果你想要获取底层数组，使用 all 方法。
toJson()

toJson 方法将集合转化为 JSON：

$collection = collect(['name' => 'Desk', 'price' => 200]);

$collection->toJson();

// '{"name":"Desk","price":200}'
transform()

transform 方法迭代集合并对集合中每个数据项调用给定回调。集合中的数据项将会被替代成从回调中返回的值：

$collection = collect([1, 2, 3, 4, 5]);

$collection->transform(function ($item, $key) {
    return $item * 2;
});

$collection->all();

// [2, 4, 6, 8, 10]
注意：不同于大多数其它集合方法，transform 修改集合本身，如果你想要创建一个新的集合，使用 map 方法。
union()

union 方法添加给定数组到集合，如果给定数组包含已经在原来集合中存在的犍，原生集合的值会被保留：

$collection = collect([1 => ['a'], 2 => ['b']]);

$union = $collection->union([3 => ['c'], 1 => ['b']]);

$union->all();

// [1 => ['a'], 2 => ['b'], [3 => ['c']]
unique()

unique 方法返回集合中所有的唯一数据项， 返回的集合保持原来的数组键，在本例中我们使用 values 方法重置这些键为连续的数字索引 ：

$collection = collect([1, 1, 2, 2, 3, 4, 2]);

$unique = $collection->unique();

$unique->values()->all();

// [1, 2, 3, 4]
处理嵌套数组或对象时，可以指定用于判断唯一的键：

$collection = collect([
    ['name' => 'iPhone 6', 'brand' => 'Apple', 'type' => 'phone'],
    ['name' => 'iPhone 5', 'brand' => 'Apple', 'type' => 'phone'],
    ['name' => 'Apple Watch', 'brand' => 'Apple', 'type' => 'watch'],
    ['name' => 'Galaxy S6', 'brand' => 'Samsung', 'type' => 'phone'],
    ['name' => 'Galaxy Gear', 'brand' => 'Samsung', 'type' => 'watch'],
]);

$unique = $collection->unique('brand');

$unique->values()->all();

/*
    [
        ['name' => 'iPhone 6', 'brand' => 'Apple', 'type' => 'phone'],
        ['name' => 'Galaxy S6', 'brand' => 'Samsung', 'type' => 'phone'],
    ]
*/
你还可以指定自己的回调用于判断数据项唯一性：

$unique = $collection->unique(function ($item) {
    return $item['brand'].$item['type'];
});

$unique->values()->all();

/*
    [
        ['name' => 'iPhone 6', 'brand' => 'Apple', 'type' => 'phone'],
        ['name' => 'Apple Watch', 'brand' => 'Apple', 'type' => 'watch'],
        ['name' => 'Galaxy S6', 'brand' => 'Samsung', 'type' => 'phone'],
        ['name' => 'Galaxy Gear', 'brand' => 'Samsung', 'type' => 'watch'],
    ]
*/
values()

values 方法通过将集合键重置为连续整型数字的方式返回新的集合：

$collection = collect([
    10 => ['product' => 'Desk', 'price' => 200],
    11 => ['product' => 'Desk', 'price' => 200]
]);

$values = $collection->values();

$values->all();

/*
    [
        0 => ['product' => 'Desk', 'price' => 200],
        1 => ['product' => 'Desk', 'price' => 200],
    ]
*/
when()

when方法在传入的第一个参数执行结果为true时执行给定回调：

$collection = collect([1, 2, 3]);

$collection->when(true, function ($collection) {
    return $collection->push(4);
});

$collection->all();

// [1, 2, 3, 4]
where()

where 方法通过给定键值对过滤集合：

$collection = collect([
    ['product' => 'Desk', 'price' => 200],
    ['product' => 'Chair', 'price' => 100],
    ['product' => 'Bookcase', 'price' => 150],
    ['product' => 'Door', 'price' => 100],
]);

$filtered = $collection->where('price', 100);

$filtered->all();

/*
[
    ['product' => 'Chair', 'price' => 100],
    ['product' => 'Door', 'price' => 100],
]
*/
检查数据项值时 where 方法使用松散条件约束。使用 whereStrict 方法过滤严格约束。

whereStrict()

该方法和 where 用法一样，不过，所有值都使用严格约束方式进行比较。

whereIn()

whereIn 方法通过包含在给定数组中的键值对集合进行过滤：

$collection = collect([
    ['product' => 'Desk', 'price' => 200],
    ['product' => 'Chair', 'price' => 100],
    ['product' => 'Bookcase', 'price' => 150],
    ['product' => 'Door', 'price' => 100],
]);

$filtered = $collection->whereIn('price', [150, 200]);

$filtered->all();

/*
[
    ['product' => 'Bookcase', 'price' => 150],
    ['product' => 'Desk', 'price' => 200],
]
*/
whereIn 方法在检查数据项值的时候使用松散的约束，要使用严格的约束可以使用 whereInStrict 方法。

whereInStrict()

该方法和 whereIn 使用方法相同，不同之处在于 whereInStrict 在比较值的时候使用严格的约束。

zip()

zip 方法在与集合的值对应的索引处合并给定数组的值：

$collection = collect(['Chair', 'Desk']);

$zipped = $collection->zip([100, 200]);

$zipped->all();

// [['Chair', 100], ['Desk', 200]]