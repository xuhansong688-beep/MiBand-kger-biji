X = poly(0,"X")
默认与X = poly(0,"X","roots")相同
如果要系数向量的话
是X = poly(0,"X","coeff")

运算时X.^2=矩阵的每一个分别相乘
没有.代表要遵循正常的矩阵运算法则

//plot(t,T,'r',t,t.^3+1,'*')1.绘制(t,T)红色*线 2.绘制t^3+1*线


**Introduction**
Ce TP a pour but d’utiliser Scilab afin d’appliquer les méthodes d’interpolation polynomiale et d’intégration et dérivation numériques à des problèmes réels.

下面三张图分别是le polynôme d’interpolation de Newton的代码，和课程中学到的方法，les points d’interpolation et le graphe du polynôme.
Les trois figures suivantes représentent respectivement le code du polynôme d’interpolation de Newton, la méthode apprise en cours, ainsi que les points d’interpolation et le graphe du polynôme.

![[Pasted image 20251023202003.png]]
Figure 1 : Code d’interpolation de Newton et le polynôme

Figure 2 : 

可以发现插值多项式很好地拟合了数据，但在插值节点附近出现了一些波动和震荡的法语
On peut observer que le polynôme d’interpolation ajuste bien les données, mais des fluctuations et des oscillations apparaissent près des points d’interpolation.

