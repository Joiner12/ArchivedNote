# MATLAB变量：global、local、persistent

matlab中，不同的变量有不同的生命周期和有效范围(工作区)。编写复杂功能matlab程序中，为了实现相关的功能，需要使用不同类型的变量，在代码运行中，也同时会涉及到不同的工作区间之间的数据共享。变量生命周期和有效范围由变量的类型决定。

## 1、基础和函数工作区

MATLAB中为了保护数据的完整性，设计了函数工作区、基础工作区和其他工作区(workspace)。

### 1.1、基础工作区

基础工作区存储命令行创建的变量以及通过命令行或者编辑器运行的脚本中创建的变量所在的内存；

### 1.2、函数工作区

每个函数有独立的函数工作区，即使普通函数的`局部函数`也有其独立的工作区。每个函数工作区中的变量都为局部变量。函数调用脚本时，使用的是函数工作区。

### 1.3、嵌套函数

嵌套函数：在函数内部定义的函数。

嵌套函数也有其自身的工作区，同局部函数不同的是：

- 嵌套函数可以对其所在的函数的变量进行读写；
- 嵌套函数或包含嵌套函数的函数中的所有变量都需要显式定义；

## 2、在工作区之间共享数据

Matlab中，不同的工作区之间共享数据的方式有以下几种：

- [ ] 传递参数 (parameter)
- [ ] 嵌套函数 (nested function)
- [ ] 持久性变量 (persistent)
- [ ] 全局变量 (global)
- [ ] 在另一工作区中计算

### 2.1、传递参数

扩大函数作用域的最安全的方式是通过函数输入、输出参数；

### 2.2、嵌套函数${^{[1]}}$

嵌套函数可以访问其所在的所有函数的工作区。

```matlab
function NestedParentFcn()
	disp('parent function');
	VarX = 0;
    NestedFcn;
    function NestedFcn()
        VarX = VarX + 1;
        VarY = 1;
    end
    try
    	%警告: 文件:LiveEditorEvaluationHelperESectionEval.mlx 行:11 列:9
		%在嵌套函数中定义 "VarY" 会将其与父函数共享。在以后的版本中，
		%要在父函数和嵌套函数之间共享 "VarY"，请在父函数中显式定%义它。 

        disp(VarY)
    catch
        disp('Access Variable VarY Error');
    end
end
```



### 2.3、持久性变量

- 持久性变量是声明该变量的局部变量；
- 其值保存在每次调用声明持久变量函数的内存中，类似于C语言中的`static local var`；
- 命令行或其他函数不能访问持久变量；
- 持久变量和全局变量类似，其值都一直保存于内存中，差异在于其可见性范围；



**syntax**

```matlab
%% example-persistent
clear varP; % 清除persistent变量
for i = 1:1:10
PersistentFcn();
end
function PersistentFcn()
persistent varP; % 声明persistent变量
if isempty(varP)
    varP = 1;
end
varP = varP + 1;
disp(varP);
end
```



**持久变量的使用**

- 持久变量必须先进行声明，声明时候其值初始化为[]；
- 必须先声明持久变量，才能对其进行其他引用；
- 以下操作可清除函数的持久性变量：`clear all`、`clear functionname`、编辑函数文件；



### 2.4、全局变量

全局变量是可以从函数或命令行中访问的变量。它们拥有自己的工作区，这些工作区与基础和函数工作区分开。如果多个函数都将特定的变量名称声明为`global`，则它们都共享该变量的一个副本。在任何函数中对该变量的值做任何更改，在将该变量声明为全局变量的所有函数中都是可见的。

**syntax**

```matlab
%% example-global
clc;
SetGlobalVar(10);
disp(GetGlobalVar())
ClearGlobal();
function SetGlobalVar(x)
global var_1; % 声明全局变量
var_1 = x;
end

function y=GetGlobalVar()
global var_1;
y = var_1;
end

function ClearGlobal()
clear global var_1;
disp('清除全局变量');
end
```



## Reference

[1] .[Matlab-嵌套函数](https://ww2.mathworks.cn/help/matlab/matlab_prog/nested-functions.html?s_tid=srchtitle)

[2]. [Matlab-persistent](https://ww2.mathworks.cn/help/matlab/ref/persistent.html)

[3]. [Matlab-在工作区之间共享数据](https://ww2.mathworks.cn/help/matlab/matlab_prog/share-data-between-workspaces.html)

[4]. [基础和函数工作区](https://ww2.mathworks.cn/help/matlab/matlab_prog/base-and-function-workspaces.html)