---
layout:     post
title:      "使用antlr4构建领域特定语言"
subtitle:   ""
date:       2017-12-19 11:00
author:     "kekliu"
header-img: ""
catalog:    true
tags:
    - Java
    - antlr
---


### 1. antlr 简介
antlr是指可以根据输入自动生成语法树并可视化的显示出来的开源语法分析器。ANTLR—Another Tool for Language Recognition，其前身是PCCTS，它为包括Java，C++，C#在内的语言提供了一个通过语法描述来自动构造自定义语言的识别器（recognizer），编译器（parser）和解释器（translator）的框架。

### 2. 入门
1. 安装插件：为intellij idea安装插件："ANTLR v4 grammer in"，重启
1. 添加运行时依赖：在idea中新建maven项目，在pom.xml使用<b>代码段1</b>添加依赖
1. 新建语法文件：在main/src/java中新建ANTLR4语法文件calculator.g4，将[calculator.g4][calculator.g4]的内容复制到该文件
1. 生成Java代码：右击calculator.g4，选择“Generate ANTLR Recognizer”
1. 使用生成代码解析目标语言：新建MainClass类，将<b>代码段2</b>添加到文件，编译运行

### 3. 文法可视化
打开一个.g4文件，右击一个文法名称，选择“Test Rule *”，在弹出的“ANTLR Preview”输入要解析的表达式，就可以看到解析后的语法树

### 4. 代码段
代码段1
{% highlight xml %}
<dependencies>
    <dependency>
        <groupId>org.antlr</groupId>
        <artifactId>antlr4</artifactId>
        <version>4.7.1</version>
    </dependency>
</dependencies>
{% endhighlight %}
代码段2
{% highlight Java %}
import grammer.*;
import org.antlr.v4.runtime.CommonTokenStream;
import org.antlr.v4.runtime.ANTLRInputStream;

public class MainClass {
    public static void main(String[] args) throws Exception {
        String expr = "1+3*sqrt(cos(0)";
        //对每一个输入的字符串，构造一个 ANTLRStringStream 流 in
        ANTLRInputStream in = new ANTLRInputStream(expr);
        //用 in 构造词法分析器 lexer，词法分析的作用是产生记号
        calculatorLexer lexer = new calculatorLexer(in);
        //用词法分析器 lexer 构造一个记号流 tokens
        CommonTokenStream tokens = new CommonTokenStream(lexer);
        //再使用 tokens 构造语法分析器 parser,至此已经完成词法分析和语法分析的准备工作
        calculatorParser parser = new calculatorParser(tokens);
        //最终调用语法分析器的规则 prog，完成对表达式的验证
        parser.expression();
    }
}
{% endhighlight %}

[calculator.g4]: https://raw.githubusercontent.com/antlr/grammars-v4/master/calculator/calculator.g4