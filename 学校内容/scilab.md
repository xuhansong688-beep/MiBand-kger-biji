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
![[Pasted image 20251023202201.png]]
Figure 2 : l'algorithme appris en cours
![[Pasted image 20251023202254.png]]
Figure 3 : les points d’interpolation et le graphe du polynôme
可以发现插值多项式很好地拟合了数据，但在插值节点附近出现了一些波动和震荡的法语
On peut observer que le polynôme d’interpolation ajuste bien les données, mais des fluctuations et des oscillations apparaissent près des points d’interpolation.

2Par calcul, on obtient les résultats suivantes
![[Pasted image 20251023202434.png]]
Figure 4:la vitesse du véhicule
我们可以发现，在2.5s和42.5s的时候，速度值较为精准，在52s的地方，产生了严重的失真。我认为这是由于我们得到的多项式P大于45s后产生的巨大震荡有关
On peut observer que les valeurs de vitesse sont relativement précises à 2,5 s et 42,5 s, tandis qu'une distorsion grave apparaît autour de 52 s. Je pense que cela est lié aux grandes oscillations du polynôme P que nous avons obtenu après 45 s.



