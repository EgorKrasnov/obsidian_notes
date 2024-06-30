You can make independent variable more 'linear' by [[Tukey's Ladder of Transformations]]
For make relation more "linear" you can take ln(variable) / log(variable)
After Tukeys transformation coefficient near b1 mean 
How much percent you should add to y when x rised by N percent. 
For example 
y = intercept + b1\*x
log(y) = intercept + b1\*log(x)
So if you add 2 percent to x then y will rise by b1\*2 percent

Before apply linear regression make sure that your linear model error is [[Homoscedasticity]]

Your data should be 
1. linear
2. with normal distribution [[Shapiro–Wilk test]]
3. and linear model error should be homoscedasticity [[Breusch-Pagan test]] or [[White test]]
![[Pasted image 20240529190652.png]]
![[Pasted image 20240529190746.png]]

[[Durbin–Watson statistic]]


If any of this conditions is False then do one of this transformations
1. [[Tukey's Ladder of Transformations]] 
2. [[Box-Cox transformation]]