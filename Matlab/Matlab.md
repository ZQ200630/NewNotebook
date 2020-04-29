## 基础函数
```matlab
matlablinspace(start_var, end_var, n)   %生成n位一维等差数组
logspace(start_var, end_var, n)  %生成n位一维等比数组
cat(n, A1, A2, A3,...,An)  %n 表示维度， An表示各维度的数组，生成一个n维数组
repmat(A, [x, x, x])  %在各个维度复制A得到矩阵
    repmat(A, x, y)  %x大行y大列以A为单元的矩阵
    reshape(A, [m, n, p]) %重组矩阵，后三参表示重组大小
zeros(m, n)  %m行n列的0矩阵
    ones(m, n)  %m行n列的1矩阵
    eye(n)  %n行n列单位阵
    magic(n)  %n阶魔方阵(由1-n方排列，每行每列对角线和相等)
    randon('name', a, b, c, d)  %生成name分布，期望为a标准差为b, c行d列随机数
    randn(m, n, ...)  %产生一个m * n * ...的标准正态分布的矩阵
size(A)  %返回A矩阵的各维大小ndims(A)  %返回A矩阵的维数whos  %返回当前工作区各变量详细信息
    A(:, :, 1)  %矩阵A第1页全部元素A(3, 2, 2)  %矩阵A第3行第二列第二页元素A(27)  %矩阵A第27个元素
   
reshape(A, m, n)  %把矩阵A重塑为m * n矩阵
permute(A, [dims])  %交换A中的各维度
ipermute(A, [dims])  %permute的逆运算
%B = permute(A, dims); ipermute(B, dims) 返回A.
        
sum(A)  %对矩阵A列向量求和
mean(A)  %对矩阵A列向量求平均值
sin(A)  %对矩阵A每个元素求sin
eig(A)  %求矩阵A的特征值所构成的向量
    
sym('[]')  %生成符号矩阵
syms a,b,c  %定义符号变量
    
exp(A)  %若A为标量，则返回e的A次方; 若A为矩阵，则返回每个矩阵元素对应的e的n次方矩阵
expm(A)  %A必须为方阵，返回矩阵的指数矩阵
    
triu(A)  %抽取A中元素构成上三角矩阵

logm(A)  %计算A的对数并返回L值

complex(a, b)  %生成a + bi

isreal(a)  %判断a是否为复数

abs(X)  %若X为矩阵，返回每一个元素的绝对值构成的矩阵; 若X为虚数, 返回其幅值(模)

conj(Z)  %返回复数Z的共轭值

subs(替换表达式, [需要替换的元素数组], [去替换的元素数组]) %课将表达式某一部分替换为另一部分

double(C)  %把符号常量C转化为双精度浮点数
digits(D)  %设置有效位数为D的近似解精度
vpa(E)  %精确计算表达式E的值
vpa(E, D)  %计算表达式E的值并至D位
numeric(E)  %将不含变量的符号表达式E转化为double双精度浮点数值形式

factor(E)  %对表达式E因式分解
expand(F)  %将表达式F展开
collect(E)  %由findsym()确定默认变量进行系数合并
collect(E, v)  %对v的项进行合并
simplify(E)  %对E运用多种恒等式变换综合化简
simple(E)  %对E尝试多种不同简化算法化简
    [R, HOW] = simple(E)  %参数R为简化型，HOW为简化过程中的简化方法
numden(E)  %对E进行通分
    [N, D] = numden(E)  %N为通分分子，D为通分分母
horner(E)  %将E转化为嵌套形式的表达式
    
char(S)  %将其他对象转化为字符对象
pretty(E)  %以习惯的书写方式显示E
    
clc  %完全清除
clear  %清除工作区数据
    
compose(f, g)  %求复合函数

finverse(f, v)  %求反函数
finverse(f)
```

### 数据导入与导出
```matlab
load   %如果matlab.mat存在,导入matlab.mat中所有变量，若不存在则返回error
load filename X Y Z 将filename中的变量导入到工作区
load filename expr1 expr2....  %通过正则表达式指定需要导入的变量
load-ascii filename  %无论输入文件名是否包含有扩展名，将其以ASCII格式读入
load-mat filename  %无论输入文件名是否包含有扩展名，将其以mat格式读入
   
importdata('filename')  %将filename中数据导入到工作区中
importdata('filename', 'delimiter')  %将filename中数据导入工作区，以delimiter指定的符号位分隔符
   
save  %将工作区所有变量保存为matlab.mat，可与load一起使用
save('filename')  %将工作区所有变量保存为filename文件,若包含路径则存储在该路径下，否则存在当前路径
save('filename', 'var1', 'var2')  %保存指定的变量在filename指定的文件中
save('filename', '-struct', 's')  %保存结构体s中全部域作为单独变量
save('....', format)  %指定保存文件格式
   
open('filename')  %其以结构体的方式将filename导入工作区，loa直接写入变量
```

## 数据统计与分析
```matlab
mean(X)  %X为向量，返回X中个元素平均值 
mean(A)  %A为矩阵, 返回A中各列元素平均值构成的向量
mean(A, dim)  %在给出的维度内的平均值

nanmean(X)  %X为向量，返回X中出NaN外元素的算数平均值
nanmean(A)  %A为矩阵，返回A中各列除NaN外元素的算数平均值向量
   
median(X)  %X为向量，返回X中个元素平均值 
median(A)  %A为矩阵, 返回A中各列元素平均值构成的向量
median(A, dim)  %在给出的维度内的平均值

nanmedian(X)  %X为向量，返回X中出NaN外元素的中位数
nanmedian(A)  %A为矩阵，返回A中各列除NaN外元素的中位数数向量
```


### MATLAB常量
```matlab
i/j  %虚数单位
Inf/inf %正无穷
NaN %不定值，非数值量
pi %圆周率双精度表示
eps %容差变量，当某量绝对值小于此时，可以认为此量为0
Realmin/realmin %最小浮点数
Realmax/realmax %最大浮点数
```


​        