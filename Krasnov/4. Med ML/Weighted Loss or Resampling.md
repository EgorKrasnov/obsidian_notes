You can use log or ln Loss function 
log with base _10_ (**lg**) is being used to calculate the loss. One other popular choice is to use **ln**

Simple loss
$$
L =
\begin{cases}
-\log P(Y=1 | X) \text { if } y = 1 \\
-\log P(Y=0 | X) \text { if } y = 0 
\end{cases}
$$

And weighted one 

$$
L =
\begin{cases}
w_p \times -\log P(Y=1 | X) \text { if } y = 1 \\
w_n \times -\log P(Y=0 | X) \text { if } y = 0 
\end{cases}
$$
$$\text {Where } w_p = \dfrac{\text {num negative}}{\text {num total}} \text{ and } w_n = \dfrac{\text {num positive}}{\text {num total}} $$
