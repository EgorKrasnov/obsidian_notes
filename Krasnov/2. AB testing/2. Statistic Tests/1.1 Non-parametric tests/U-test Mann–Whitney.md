Framework
- Unite groups 
- Range observations
- Get the sum of ranges by groups 
- Calculate U statistic for each group
![[U-test.png]]
- Get the smallest one
- Check the table

Use U-test when 
1. When even distribution in one group isn't normal
2. When there are outliers in groups
Don't use 
1. When groups has different size

Примером рангового теста служит критерий Манна-Уитни. Отметим, что критерий Манна-Уитни проверяет иную гипотезу, чем тест Стьюдента. Если тест Стьюдента сравнивает значения средних величин, то критерий Манна-Уитни тестирует гипотезу того, что случайно выбранный объект из первой группы будет с равными шансами как больше, так и меньше случайно выбранного объекта из второй группы. Гипотеза тестируется против альтернативы, что вероятность того, что объект из первой группы больше объекта из второй группы не равна 0.5.

$H\_0: F_X(x) = F_Y(x)$
$H\_1: F_X(x) = F_Y(x + δ), \delta \neq 0$
