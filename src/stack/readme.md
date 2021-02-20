栈是一个先入后出(FILO-First In Last Out)的有序列表。

- 栈(stack)是限制线性表中元素的插入和删除只能在线性表的同一端进行的一种特殊线性表。允许插入和删除的
  一端，为变化的一端，称为栈顶(Top)，另一端为固定的一端，称为栈底(Bottom)。
- 根据栈的定义可知，最先放入栈中元素在栈底，最后放入的元素在栈顶，而删除元素刚好相反，最后放入的元素最先删除，最先放入的元素最后删除
- 子程序的调用：在跳往子程序前，会先将下个指令的地址存到堆栈中，直到子程序执行完后再将地址取出，以
  回到原来的程序中。
- 处理递归调用：和子程序的调用类似，只是除了储存下一个指令的地址外，也将参数、区域变量等数据存入堆
  栈中。
- 表达式的转换[中缀表达式转后缀表达式]与求值(实际解决)。
- 二叉树的遍历。
- 图形的深度优先(depth 一 first)搜索法

**词法分析**

单词表：+，-，*，/，（，）

工具：有限自动状态机

**语法分析**

表达式BNF产生式：

exp -> or | or = exp;

or ->  and | or || and;

and -> equal | and && equal;

equal -> rel | equal == rel | equal != rel;

rel -> add | rel > add | rel < add | rel >= add | rel <= add;

add -> mul | add + nul | add - mul;

mul -> pri | mul * pri | mul / pri;

pri -> Id | Literal | (exp);

工具：上下文无关文法

**语法分析实例**

1.**开始**：

------

S -> exp

exp -> exp opt exp

exp -> (exp) | alphabet

opt -> + | - | * | /

推导：(A-B)*C

推导：A-B-C（二义性）

(A-B)-C---结合

A-(B-C)---错误，需要消除

2.**消除歧义**

------

可以看到上面同一种语法规则，可以产生两种不同的解析路径，而不同的解析路径可能会造成语言表达上的二义性。

有三种方式消除歧义：

- [ ] 限制用户输入的自由度，不接受可能的二义性输入
- [x] 改造语法表达式，以消除二义性
- [ ] 在代码中为二义性情况做特殊化处理，确保解析路径正确

S -> exp

exp -> exp opt item | item

item -> (exp) | alphabet

opt -> + | -  | * | /

推导：A-B-C

推导：A-B*C---错误，乘法的优先级高减法的优先级

3.**优先级**

**叶子节点的优先级最高，即首先被计算**

S -> AddExp

AddExp -> AddExp opt1 MulExp | MulExp

opt1 -> + | -

MulExp ->  MulExp opt2 AtoMicExp | AtoMicExp

opt2 -> * | /

AtomicExp -> (AddExp) | alphabet

保证的优先级**（+，-） < (*,/) < 括号表达式**

**递归下降算法（消除无限左递归）**

------

S -> AddExp

AddExp -> MulExp opt1 AddExp | MulExp

opt1 -> + | -

MulExp -> AtomicExp opt2 MulExp | AtomicExp

opt2 -> * | /

AtomicExp -> (AddExp) | alphabet

**有递归，导致效率下降**

1. 确定各个分支的FIRST集，选择FIRST集中包含当前token的分支
2. 改造BNF为EBNF，用循环代替递归

AddExp -> AddExp opt1 MulExp | MulExp

AddExp -> MulExp {opt1 MulExp}

```javascript
function AddExp() {
    let node = MulExp();
    while(opt1()) {
        node = {
            opt: tokens[index - 1].lexeme, // 上一个token是运算符
            leftNode: node,
            rightNode: MulExp(),
        };
    }
    return node;
}
```














