---
title: Markdown 表格生成
tags: [Productivity]
date: 2018-06-29 21:39:19
updated: 2019-08-31 14:55:48
blogged: true
urlname: Markdown-Tables
---
Markdown 对表格的支持不太友好,这里列了几个处理的技巧。

## 插入表格

在Markdown文本中插入表格主要有以下三种方法：

1. 标准Markdown table语法

    ```markdown
    |  A   |    B | C    |
    | :--: | ---: | :--- |
    |  1   |    2 | 3    |
    |  4   |    5 | 6    |
    ```
	
	效果：
	
    |  A   |    B | C    |
    | :--: | ---: | :--- |
    |  1   |    2 | 3    |
    |  4   |    5 | 6    |
    
2. 嵌入html代码

    ```html
    <table style="text-align:center">
      <tr>
          <td>A</td>
          <td>B</td>
      </tr>
      <tr>
          <td>C</td>
          <td>D</td>
      </tr>
    </table>
    ```

    效果：

    <table style="text-align:center">
      <tr>
          <td>A</td>
          <td>B</td>
      </tr>
      <tr>
          <td>C</td>
          <td>D</td>
      </tr>
    </table>

3. Latex 语法

    - Typora等编辑器可借由MathJax插入数学公式，其中`\array`可用来插入表格
    
    - 在Hexo等博客框架中也可以利用MathJax插入Latex，不过渲染工具要换为Pandoc
    
        ```bash
        brew install pandoc
        npm uninstall hexo-renderer-marked --save
        npm install hexo-renderer-pandoc --save
        ```
    
    ```latex
    $$
    \begin{array}{ccc} % c:center, r:right, l:left
    \hline
    姓名& 学号& 性别\\
    \hline
    Steve Jobs& 001& Male\\
    Bill Gates& 002& Female\\
    \hline
    \end{array}
    $$
    ```
    
    效果：
    $$
    \begin{array}{ccc} % c:center, r:right, l:left
    \hline
    姓名& 学号& 性别\\
    \hline
    Steve Jobs& 001& Male\\
    Bill Gates& 002& Female\\
    \hline
    \end{array}
    $$

## 跨行/跨列表格 columnspan/rowspan

1. html语法

    ```
    <table style="text-align:center">
       <tr>
          <td colspan='2' rowspan='2'></td>
          <td colspan='2' >真实类别<br>Truth</td>
      </tr>
      <tr>
          <td>1 (Event)</td>
          <td>0 (Non-Event)</td>
      </tr>
      <tr>
          <td rowspan='2'>预测类别<br>Prediction</td>
          <td>1<br>Positive</td>
          <td>True<br>Positive</td>
          <td>False<br>Positive</td>
      </tr>
      <tr>
          <td>0<br>Negative</td>
          <td>False<br>Negative</td>
          <td>True<br>Negative</td>
      </tr>
    </table>
    ```

    效果:

    <table style="text-align:center" class="table-bordered table-striped table-condensed">
      <tr>
          <td colspan='2' rowspan='2'>Example</td>
          <td colspan='2' >真实类别<br>Truth</td>
      </tr>
      <tr>
          <td>1 (Event)</td>
          <td>0 (Non-Event)</td>
      </tr>
      <tr>
          <td rowspan='2'>预测类别<br>Prediction</td>
          <td>1<br>Positive</td>
          <td>True<br>Positive</td>
          <td>False<br>Positive</td>
      </tr>
      <tr>
          <td>0<br>Negative</td>
          <td>False<br>Negative</td>
          <td>True<br>Negative</td>
      </tr>
    </table>

2. Latex语法：

    - 使用array嵌套排列

        ```latex
        $$
        \begin{array}{c|c}
        a^2-b^2 & 
        \begin{array}{ccc} 1+i & 1-i & \frac{1}{\sqrt{2}}
        \end{array} \\\hline  
        \begin{array}{cc}a-b &  a+b
        \end{array} & 
        
        \begin{array}{c|c}
          A & \begin{array}{ccc} a & b & c \end{array}\\
          \hline
          \begin{array}{cc} X &  Y \end{array} & Z
        \end{array}
        
        \end{array}
        $$
        ```

        效果：
        $$
        \begin{array}{c|c}
        a^2-b^2 & \begin{array}{ccc} 1+i & 1-i & \frac{1}{\sqrt{2}} \end{array} \\\hline
        \begin{array}{cc}a-b & a+b \end{array} & 
            \begin{array}{c|c}
              A & \begin{array}{ccc} a & b & c \end{array}\\
              \hline
              \begin{array}{cc} X &  Y \end{array} & Z
            \end{array}
        \end{array}
        $$
        
    - 使用`rlap`
    
        ```latex
        \begin{array}{r|lll}
        & \rlap{\text{number of foo}} \\
        \text{number of bar} & 0 & 1 & 2 \\
        \hline
        0 & 0.125 & 0.250 & 0.168 \\
        1 & 0.125 & 0.250 & 0.168 \\
        2 & 0.125 & 0.250 & 0.168
        \end{array}
        ```
    
        效果：
        $$
        \begin{array}{r|lll}
        & \rlap{\text{number of foo}} \\
        \text{number of bar} & 0 & 1 & 2 \\
        \hline
        0 & 0.125 & 0.250 & 0.168 \\
        1 & 0.125 & 0.250 & 0.168 \\
        2 & 0.125 & 0.250 & 0.168
        \end{array}
        $$

## 快速插入

- 多种格式的表格的生成与相互转换
    - [http://tablesgenerator.com](http://tablesgenerator.com/markdown_tables)
    - [https://tableconvert.com](https://tableconvert.com/) (Thanks @**ayayo** for the recommendation.)
    - [http://pressbin.com/tools/excel_to_html_table/index.html](http://pressbin.com/tools/excel_to_html_table/index.html)



## 其他

- 表格内换行：插入`<br>`换行。
- 表格样式调整与优化：参考->[这篇](http://moxfive.xyz/2016/03/04/markdown-table-style/)