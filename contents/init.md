#the first part is __init__ moduel

```
 def __init__(self, x, y, w, transformation="r", permutations=PERMUTATIONS,
                 geoda_quads=False):
        x = np.asarray(x).flatten()
```
turn everything into one dimension

```             
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
__quads is a private method and if u want to read its source code please tab here[quad]()

```
if permutations:# this function can generate the p-value
            self.__crand()
            sim = np.transpose(self.rlisas)
            
```
only after introduce the above methods, can the self.rlisa be used
```
            above = sim >= self.Is
```            
here is the boradcast of the numpy ,            这里也属于数组的广播，（999，1519）与（一个一维数组或者二维1*n或者列表才能进行广播，且这两个被比较的数组的列数需要相等），这一点一定要记住！！！！！！(to be continued)
```
            larger = above.sum(0)
```
sum the outcome where the permutation values is larger than the random values
```
            low_extreme = (self.permutations - larger) < larger
            larger[low_extreme] = self.permutations - larger[low_extreme]
            self.p_sim = (larger + 1.0) / (permutations + 1.0)
```            
	    the above sentence is the core part of the method calculating the
p-value, untill now i still can not figure out principle behind the code, and the principle (to be continued)
	 
```
            self.sim = sim
            self.EI_sim = sim.mean(axis=0)
            self.seI_sim = sim.std(axis=0)
            self.VI_sim = self.seI_sim * self.seI_sim
            self.z_sim = (self.Is - self.EI_sim) / self.seI_sim
            self.p_z_sim = 1 - stats.norm.cdf(np.abs(self.z_sim))
```











