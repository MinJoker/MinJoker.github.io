# 课程笔记

!!! quote "引用其他人的笔记"

    此「课程笔记」作为对以下笔记的补充：

    [聆诡思谛的语雀/一些浙大课程笔记/离散数学及其应用](https://www.yuque.com/linguisty/zju_courses/yx9t7q)

## The Foundations: Logic and Proofs

### 命题逻辑补充

- 来一些中英对照（主要是我觉得这些中文翻译还蛮有意思）：
    - 合取（Conjunction）
    - 析取（Disjunction）
    - 蕴含（Implication）
- 关于蕴含值得注意的是，Everything implies `TRUTH`, `FALSE` implies anything.
- 再来一些中英对照：
    - 永真式/重言式（Tautology）
    - 永假式/矛盾式（Contradiction）

!!! note "一些很重要的公式"

    $$
    p\to q \iff \neg p \vee q
    $$

    $$
    p\leftrightarrow q \iff (p\to q) \wedge (q\to p)
    $$

### If 与 Only if

$p\to q$ 的两个等价叙述：

1. `q` if `p`;
2. `p` only if `q`;

关于 If 与 Only if 的理解：

- If 表示充分（sufficient）条件，相当于“当”
- Only if 表示必要（necessary）条件，相当于“仅当”
- If and only if（Iff）表示充要条件，相当于“当且仅当”

### 离散与数逻的表达式范式

离散数学里的表达式范式与数字逻辑设计里表达式的标准形式和规范形式是一致的，但是两者对此有不同的称呼：

- 标准形式（不唯一）：
    - Disjunctive Normal Form（DNF）（析取范式）与 SOP 一致
    - Conjunctive Normal Form（CNF）（合取范式） 与 POS 一致
- 规范形式（唯一）：
    - Full Disjunctive Normal Form（主析取范式）与 SOM 一致
    - Full Conjunctive Normal Form（主合取范式）与 POM 一致

关于标准形式化成规范形式的技巧、两种范式的对称性及其转化，请跳转至[数字逻辑设计相应章节](/cs/digital_logic/note0/#22)学习。

### 推理的技巧

技巧一：如果结论是 $p\to q$ 的形式，那么我们可以把原问题像这样转化：

$$
(p_1\wedge p_2\wedge ... \wedge p_n)\to(p\to q) \iff (p_1\wedge p_2\wedge ... \wedge p_n \wedge p)\to q
$$

---

技巧二（反证法）：假设推理是 $q$， 那么我们可以这样反证：

$$
\begin{aligned}
((p_1\wedge p_2\wedge ... \wedge p_n)\to q)^{(\*)} & \iff \neg (p_1\wedge p_2\wedge ... \wedge p_n)\vee q \cr
& \iff \neg (p_1\wedge p_2\wedge ... \wedge p_n\wedge \neg q)^{(\*\*)}
\end{aligned}
$$

(\*) 是重言式，当且仅当 (\*\*) 是矛盾式。

---

技巧三（归结原理，Resolution）：一个很有意思但是不太容易被察觉的推理公式：

$$
(p\vee q)\wedge (\neg p\vee r)\to (q\vee r)
$$

更进一步地，基于任何表达式都可以化成合取范式这一事实，归结原理是「自动定理证明」的一种重要推理规则。