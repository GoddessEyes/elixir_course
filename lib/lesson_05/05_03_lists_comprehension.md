## Конструкторы списков

Конструкторы списков (lists comprehension) -- еще один высокоуровневый
способ работы со списками.

Синтаксис напрямую заимствуется из математики. Например,
математическое выражение:

```
{x | x ∈ N, x > 0}
```

означает: из множества натуральных чисел (N), взять каждый элемент (x), больший нуля.

Соответствующее ему выражение в эрланг:

```
1> List = [-2,-1,0,1,2].
[-2,-1,0,1,2]
2> [X || X <- List, X > 0].
[1,2]
```

Из списка List взять каждый элемент, больший нуля.

Конструкторы списков делают то же, что map и filter. filter мы уже
увидели, а вот map:

```
3> [X * 2 || X <- List].
[-4,-2,0,2,4]
```

Конечно, можно объединять и то, и другое в одном проходе:

```
4> [X * 2 || X <- List, X > 0].
[2,4]
```

Но в отличие от функций lists:map/2, lists:filter/2, они могут
работать с несколькими списками одновременно:

```
5> List1 = [1,2].
[1,2]
6> List2 = [a,b].
[a,b]
7> List3 = [cc,dd].
[cc,dd]
8> [{X,Y,Z} || X <- List1, Y <- List2, Z <- List3].
[{1,a,cc},
 {1,a,dd},
 {1,b,cc},
 {1,b,dd},
 {2,a,cc},
 {2,a,dd},
 {2,b,cc},
 {2,b,dd}]
```

Как видно, элементы списков соединяются "каждый с каждым".

Ну и, конечно, все эти списки можно фильтровать:

```
9> [{X,Y,Z} || X <- List1, X > 1, Y <- List2, Y =/= a, Z <- List3].
[{2,b,cc},{2,b,dd}]
```

Если элементы списка -- сложные структуры, то можно извлекать значения
внутри них, и вычислять что-то на основе этих внутренних
значений. Например, вот так вычисляем площади прямоугольников:

```
1> List = [{rect, 5, 10}, {rect, 4, 8}, {rect, 4, 3}].
[{rect,5,10},{rect,4,8},{rect,4,3}]
2> [{area, W * H} || {rect, W, H} <- List].
[{area,50},{area,32},{area,12}]
```

И фильтровать можно по внутренним значениям и по производным от них выражениям:

```
3> [{area, W * H} || {rect, W, H} <- List, W * H < 40].
[{area,32},{area,12}]
```

Давайте опять вернемся к нашему списку пользователей:

```
get_users() ->
    [{user, 1, "Bob", male, 22},
     {user, 2, "Helen", female, 14},
     {user, 3, "Bill", male, 11},
     {user, 4, "Kate", female, 18}].
```

Отфильтруем по полу с помощью конструкторов списков:

```
[User || {user, _, _, Gender, _} = User <- Users, Gender =:= female].
```

И извлечем {Id, Name} так же:

```
[{Id, Name} || {user, Id, Name, _, _} <- Users].
```

Ну и напоследок красивый пример из книги Джо Армстронга с пифагоровыми
тройками.  Вспомним теорему Пифагора: сумма квадратов катетов равна
квадрату гипотенузы.  Существует не так много вариантов, когда длины
катетов и гипотенузы выражаются целыми числами. Самый известный такой
вариант: {3, 4, 5}.

Армстронг предлагает найти все такие варианты с помощью конструкторов
списков.  На входе дана максимальная длина гипотенузы, на выходе нужно
получить список всех возможных троек **{Катет, Катет, Гипотенуза}**,
где длины являются целыми числами.

Берем список всех возможных длин:

```
1> Max = 20.
20
2> Lengthes = lists:seq(1, Max).
[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20]
```

И генерируем все возможные сочетания длин:

```
3> [{X,Y,Z} || X <- Lengthes, Y <- Lengthes, Z <- Lengthes].
[{1,1,1},
 {1,1,2},
 {1,1,3},
 ...
```

Промежуточный результат получится очень большой, но это не важно. Дальше его нужно отфильтровать.

```
4> [{X,Y,Z} || X <- Lengthes, Y <- Lengthes, Z <- Lengthes, X * X + Y * Y =:= Z * Z].
[{3,4,5},
 {4,3,5},
 {5,12,13},
 {6,8,10},
 {8,6,10},
 {8,15,17},
 {9,12,15},
 {12,5,13},
 {12,9,15},
 {12,16,20},
 {15,8,17},
 {16,12,20}]
```

Хорошо бы еще устранить дубликаты. Для этого добавим еще один фильтр.

```
5> [{X,Y,Z} || X <- Lengthes, Y <- Lengthes, Z <- Lengthes, X < Y, X * X + Y * Y =:= Z * Z].
[{3,4,5},{5,12,13},{6,8,10},{8,15,17},{9,12,15},{12,16,20}]
```

Задача решена одной строкой кода :)