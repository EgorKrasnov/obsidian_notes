> Дисперсионный анализ (ANOVA) – это статистическая процедура, используемая для сравнения средних значений определенной переменной в двух и более независимых группах 
> [[Статистика для всех, Бослаф С.]]

> Предположим, что вместо A/B-теста мы сравнивали многочисленные группы, скажем, A-B-C-D, где каждая содержит **числовые данные**. Статистическая процедура, которая выполняет проверку на статистически значимую разницу среди групп, называется дисперсионным анализом, или ANOVA (от англ. analysis of variance).![[Pasted image 20240603030823.png]]
> [[Практическая статистика для специалистов Data Science]]


Requirements:
1. Normal distribution 
	- [[Shapiro–Wilk test]] or [[Kolmogorow-Smirnow test]] or [[Anderson–Darling test]]
	- QQ plot
2. Homogeneity of Variance (optional)
	- [[Levene's test]]  

After this just use [[F-test]]
Or use non-parametric [[Kruskal–Wallis test]]

![[Pasted image 20240529191349.png]]

![[Pasted image 20240529191438.png]]

If there is more then one group and F-statistic is stat important then use aposterior test like [[Scheffe test]]