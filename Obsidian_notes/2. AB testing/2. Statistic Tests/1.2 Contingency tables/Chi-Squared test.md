[Theoretical materials](http://bioinformatics.ru/Data-Analysis/Pearson_chisquare_distance.html)
[Chi-square and F-test comparison](https://saylordotorg.github.io/text_introductory-statistics/s15-chi-square-tests-and-f-tests.html)

The chi-square distribution arises in tests of hypotheses concerning the independence of two random variables and concerning whether a discrete random variable follows a specified distribution.
### Uses of Chi-square test
> Критерий хи-квадрат – это один из наиболее распространенных способов изучения связей между двумя и более категориальными переменными. Проведение этого теста включает расчет статистики хи-квадрат и ее сравнение с распределением хи-квадрат, чтобы найти вероятность данного результата критерия. 
> Нулевая гипотеза состоит в том, что распределение какой-то переменной одинаково во всех генеральных совокупностях
> Критерий согласия хи-квадрат используют для проверки гипотезы о том, что распределение какой-либо категориальной переменной в генеральной совокупности совпадает с заданным распределением, тогда как альтернативная гипотеза гласит, что распределение этой переменной какое-то иное, но не предполагаемое.
> [[1. Books/Статистика для всех, Бослаф С./0. cover]]

![[Pasted image 20240528170724.png]]
![[Pasted image 20240528170742.png]]

![[Pasted image 20240528170906.png]]

![[Pasted image 20240529192933.png]]


### Tests for Independence
> Критерий хи-квадрат Пирсона подходит для данных, в которых все наблюдения независимы

### Yates's correction for continuity
[[Yates's correction for continuity]]
### Main idea
$$\chi^2 = \sum_{i=1}^{n}\dfrac{(O_i - E_i)^2}{E_i}$$
Where O is **Observed** and E is **Expected**
Chi-Square distribution
![[Chi-Square distribution.png|500]]

Critical value X^2 for p < 0.05 with degrees of freedom = 2 is **5.9** 
Degrees of freedom = number of columns in Contingency table - 1 

Some examples
H0:Baby gender and baby heart rate are independent
vs. 
Ha:Baby gender and baby heart rate are not independent
![[baby heart rate.png]]

