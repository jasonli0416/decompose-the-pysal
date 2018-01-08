# decompose-the-pysal

## the picture showing below is Pro. Luc Anselin

![luc anselin](/pics/anselin.jpeg)
>Author: libowen

>e-mail: 437147838@qq.com

>start Date: 2018/01/07

---

PAY ATTENTION: I am also a rookie for python and pysal， if there are some mistakes in the belowing contents, please, help me optimizing this

---

## here i will first show you the source code of bivariate moran's I which is also my most commonly used moduel

-the first part is init moduel

```
 def __init__(self, x, y, w, transformation="r", permutations=PERMUTATIONS,
                 geoda_quads=False):
        x = np.asarray(x).flatten()# turn everything into one dimension
        y = np.asarray(y).flatten()
        self.y = y
        n = len(y)
        self.n = n
        self.n_1 = n - 1
        zx = x - x.mean()
        zy = y - y.mean()
        # setting for floating point noise
        orig_settings = np.seterr()
        np.seterr(all="ignore")
        sx = x.std()#standardlized the x
        zx /= sx
        sy = y.std()
        zy /= sy
        np.seterr(**orig_settings)
        self.zx = zx
        self.zy = zy
        w.transform = transformation
        self.w = w
        self.permutations = permutations
        self.den = (zx * zx).sum()
        self.Is = self.calc(self.w, self.zx, self.zy)
        self.geoda_quads = geoda_quads
        quads = [1, 2, 3, 4]
        if geoda_quads:
            quads = [1, 3, 2, 4]
        self.quads = quads
        self.__quads()
```
#this is a private method and if u want to read its source code please tab here* [quads](- __quads)
```
	if permutations:# this function can generate the p-value
            self.__crand()
            sim = np.transpose(self.rlisas)#使用了上面的方法，这个属性才能被调用,999行1519列的
            above = sim >= self.Is#这里也属于数组的广播，（999，1519）与（一个一维数组或者二维1*n或者列表才能进行广播，且这两个被比较的数组的列数需要相等），这一点一定要记住！！！！！！
            larger = above.sum(0)#求和了置换值大于推测值的数量，
            low_extreme = (self.permutations - larger) < larger
            larger[low_extreme] = self.permutations - larger[low_extreme]
            self.p_sim = (larger + 1.0) / (permutations + 1.0)
	    #这是这段代码最核心的一部分，p值和置换结果大与计算结果的数量有关。	
            self.sim = sim
            self.EI_sim = sim.mean(axis=0)
            self.seI_sim = sim.std(axis=0)
            self.VI_sim = self.seI_sim * self.seI_sim
            self.z_sim = (self.Is - self.EI_sim) / self.seI_sim
            self.p_z_sim = 1 - stats.norm.cdf(np.abs(self.z_sim))
```
- quads
