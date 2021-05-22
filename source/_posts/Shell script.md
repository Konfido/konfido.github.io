---
title: Shell Script
tags: [dev, Handbook]
typora-root-url: ..
typora-copy-images-to: ../images
date: 2019-03-14 18:13:29
updated: 2020-09-02 22:36:09
blogged: true
urlname: shell-script
---



## Symbols

- `[` single bracket

    - `if [ condition ]` shell builtin, `test` construct, used to ==evaluate expression==

        ```bash
        if [ $a == abc ] ; then echo yes ; fi
        ```

    - `Array[1]=element1` Array initializatin

    - `[a-z]` Range of characters within a Regular Expression

- `[[` double bracket

    - bash builtin, 与`[`类似，extended `test` constrcut

- `{}` braces

    - `${variable}` unambiguously ==identify variables==

        ```bash
        echo $VARIBLE1234
        echo ${VARIBLE}1234
        ```

    - `${!variable}` Indirect variable reference 间接引用

        ```bash
        NAME="VARIABLE"; VARIABLE=42; echo ${!NAME}
        # 42
        ```

    - `{ command1; command2 }` ==command group==

        ```bash
        { date; top -b -n1 | head ; } >logfile
        # `date` and `top` output are concatenated
        ```

    - brace expansions, create lists of strings，可以在loop时使用

        ```bash
        echo f{oo,ee,a}d	# food feed fad
        ```

        ```bash
        mv error.log{,.OLD}	# 等价于：mv error.log error.log.OLD
        ```

        ```bash
        echo {000..2}		# 000 001 002
        echo {00..8..3}		# 00 03 06
        echo {D..T..4} 		# D H L P T
        ```

- `()` parentheses

    - `( commands1; command2 )` ==command group== excuted within a ==subshell== (without affecting the current shell's environment)

        ```bash
        (cd /tmp; pwd)
        ```

    - `result=$(COMMAND)` Command substitution

    - `Array=(element1 element2)` Array initialization

- `(())` double parentheses

    - `(( var = 12 ))` Integer arithmetic，==整数计算==
    - `var=$(( 20 + 5 ))` 整数计算与赋值
    - C-style operation
        - `((var++))` /  `((var--))` / `((var0 = var1<12?3:21))`

- `#`：注释

## 变量

- 格式要求

    - 定义变量时，变量名不加`$`符号，使用时加​`$`
        ```bash
        test="hello"
        echo $test
        ```
    - 变量名和等号之间==不能有空格==
    - 传递带空格的参数需要加引号：`"${VARIABLE}"`
    - 单引号内任何字符不视作变量
    - 将命令结果赋值给变量：反引号 或`$()` 
        - <code>x=\`commands\`</code>
        - `x=$(commands)`
- 变量的作用域
    - Shell脚本中定义的变量是global的，作用域从被定义的地方开始
    - Shell==函数==中定义的变量默认是global的，作用域从被调用时定义处开始
    - Shell函数内显式定义local变量时，作用域在函数内；但是与global变量重名时，会在函数内暂时屏蔽global变量
        ```bash
        foo()
        {
        	local x=100
        	echo $x
        }
        foo 		# 100
        echo $x 	# show nothing
        ```
- 判断变量是否为空
    - `${A:+xxx}` 变量A已赋值时，其值用xxx (字符串/变量的值) 替换，否则不进行任何替换
    - `${A:-xxx}` 变量A==未定义/值为空==时，返回xxx (字符串/变量的值)；否则返回变量A的值
    - `${A:=xxx}` 变量A未定义/值为空时，将xxx赋值给A，返回xxx；否则返回变量A的值
    
- 特殊变量

    | $0   | 当前脚本的文件名                                             |
    | ---- | ------------------------------------------------------------ |
    | $n   | 传递给脚本或函数的参数。n 是一个数字，表示第几个参数。例如，第一个参数是`$1`，第二个参数是`$2`。 |
    | $#   | 传递给脚本或函数的**参数个数**。                             |
    | $*   | 传递给脚本或函数的**所有参数**。被双引号包含时，以`"$1"，"$2" … "$n" `的形式输出所有参数。 |
    | $@   | 传递给脚本或函数的所有参数。被双引号包含时，以`"$1 $2 … $n"`的形式输出所有参数整体。 |
    | $?   | 上个命令的退出状态，或函数的返回值。                         |
    | $$   | 当前Shell进程ID。对于 Shell 脚本，就是这些脚本所在的进程ID。 |
    | $!   | 后台运行的最后一个进程的ID号                                 |
    
    - `[@]` 引用数组中的值
    
        ```bash
        declare -a testarray=('box' 'cat' 'dog')
        for item in ${testarray[@]}; do
        	echo $item
        done
        #box
        #cat
        #dog
        ```



## 数学计算

- `let`

    ```bash
    let `sum=3+5`	# sum <- 8
    ```

- `expr`

    ```bash
    sum=`expr 3 - 6`    	# 运算符两边空格
    sum=`expr \( 3 \* \) `	# 转译*)(
    ```

- `$(())`

    ```bash
    sum=$((3+5))
    ```

    

## expression 表达式种类

- 文件表达式

    - `-d` 如果目录存在：`if [ -d .. ]` 
    - `-f` 如果文件存在：`if [ -f file ]` 
    - `-s` 文件存在且非空
    - `-r` 文件存在且可读
    - `-w` 文件存在且可写
    - `-x` 文件存在且可执行

- ==整数变量==表达式
    - `-eq` 如果等于：`if [ int1 -eq int2 ] `
    - `-ne` 如果不等于 
    - `-ge ` 如果>=
    - `-gt` 如果>
    - `-le ` 如果<=
    - `-lt ` 如果<

-  字符串变量表达式

    - `=`  等于
        - `if [ $a = $b ]  `，如果string1等于string2
        - 允许使用赋值号做等号，等号两边==必须留空格==

    - `!= `  不等于
        - `if [ $a != $b ]` 如果string1不等于string2 
    - `-z`  为空
        - `if [ -z $string ]` 如果string 为空

    - `-n`  非空
        - ``if [ -n $string ]  `` 等价于 `if [ $string]` 如果string 非空(非0），返回0(true)

- 逻辑表达式

    - `!` 逻辑非： `if [ ! expr ]` 
    - `-a` 逻辑与：`if [ expr1 -a expr2 ]`
    - `-o` 逻辑或：`if [ expr1 -o expr2 ]`



## 字符串处理

- 字符串长度/字符数量：`${#A}`

### 匹配
- 简单方法，若 `$string`中含有`$foo` 则匹配成功：

    ```bash
    [[ $string == *$foo* ]] && echo match
    ```

    ```bash
    [[ $string =~ $foo ]] && echo match
    ```

- 非贪婪匹配，匹配符合通配符的==最短结果==
    - `${A%B}` 从右向左匹配B，删除匹配，保留左边字符
    - `${A#B}` 从左向右匹配B，删除匹配，保留右边字符
    - `${A/old/new}` 用`new`替换`A`中匹配的`old`字符串
- 贪婪匹配，匹配==最长结果==
    -  `${A%%B}`
    -  `${A##B}`
    -  `${A//old/new}`

### 提取

- `grep`

    ```bash
    # -o，只输出匹配的字符串; -E，使用扩展正则
    echo aaa888 | grep -Eo '\d+'		
    # 888
    ```

- `sed`

    - sed没有`\d` `\s`等用于匹配的元字符

        ```bash
        echo aaa888s | sed -E 's/.*[a-z]([0-9]+).*/\q/'	# -E，使用正则
        # 888
        ```

- `awk` 的 `match()`函数

    ```bash
    echo aaa888 | awk 'match($0,/([0-9]+)/, matches) {print matches[1]}'
    ```

- `${A:offset}` 
  
    - 从字符串左边，偏移offset个字符，截取到字符串结束
    - `${A:offset:length}` 截取length长度
    
- `${A:0-offset}` 
  
    - 从字符串右边，偏移offset个字符，截取到字符串结束
    - `${A:0-offset:length}`
    
- String to Array

    ```bash
    DATES_STRING='Jun01 Jun02 Jun03 Jun04 Jun05'
    IFS=' ' read -a DATES_ARRAY <<< "$DATES_STRING"
    ```

### 替换

- 普通字符

    ```bash
    echo "haha s" | sed 's/ha/Ha/g'
    ```

- 替换`\n`

    ```bash
    echo $s | tr '\n' '\t'
    ```

    




## 重定向 输入/输出

|    Commands     |                    Description                     |
| :-------------: | :------------------------------------------------: |
| command > file  |                将输出重定向到 file                 |
| command < file  |                将输入重定向到 file                 |
| command >> file |          将输出以追加的方式重定向到 file           |
|    n > file     |       将文件描述符为 n 的文件重定向到 file。       |
|    n >> file    | 将文件描述符为 n 的文件以追加的方式重定向到 file。 |
|     n >& m      |              将输出文件 m 和 n 合并。              |
|     n <& m      |              将输入文件 m 和 n 合并。              |
|     << tag      | 将开始标记 tag 和结束标记 tag 之间的内容作为输入。 |

- 默认打开的文件描述符：0（标准输入），1（标准输出），2（标准错误）
- `1>`：可简写为`>`，标准输出重定向
- `2>&1`：标准错误重定向到标准输出。例：`echo hello 1>file.out 2>&1`，输出与错误都定向到file.out
- `1>&2`：标准输出重定向到标准错误
- `&>file`：标准输出与标准错误都从定向到file



## 流程控制

- `case`

    ```bash
    case $a in
        10) echo "1"
        ;;
        reload) echo "12"
        ;;
        *) echo "0"
        ;;
    esac
    ```

- `if/else`

    ```bash
    if [ $a -eq $b ]; then
        echo "="
    elif [ $a -gt $b ]; then
        echo ">"	
    else
        echo "<"
    fi
    ```
    
    - `if`语句简写
    
        ```bash
        # 如果左边表达式为真，则执行右边的语句
        [ -f "./foo" ] && echo "yes"
        # 如果左边表达式为假，则执行右边
        [ -f "./foo"] || exit 0 # 文件不存在就退出
        ```
    
    - 可用来handle exception
    
        ```bash
        # run fallback_command if a_command fails (returns a non-zero value)
        a_command || fallback_command
        # execute second_command if a_command is successful (returns 0)
        a_command && second_command
        ```
    
        
    
- `for/do`

  ```bash
  for var in 1 2 3 4 5
  do 
      echo "$var"
  done
  ```
  
- `while/do`

    ```bash
    while [ $int -lt 8 ]
    do 
        case $int in
            1|4) echo "$int"
                ;;
            0|2|5|) echo "$int"
                continue
                ;;
            *) echo "others"
                break
                ;;
        esac
    done
    ```

## function

- 函数名前的"function"可以省略
- 使用 `$?` 获取函数返回值，且调用函数后需立即获取，否则返回值丢失

    ```bash
    function foo(){
        COMMANDS
        return xxx
    }
    ```

    ```bash
    foo(){
        COMMANDS
        ...
        echo $1		# 引用参数
        return xxx	# 返回值
    }
    foo 13			# 传递参数
    ehco $?			# 获取函数返回值
    ```
    
    

## 子进程/子Shell

- 子Shelll

    - 只使用 fork() 函数，父进程中的函数、变量(全局变量、局部变量)、文件描述符、别名等在子Shell中仍然有效；子Shell对参数的修改无法传回父Shell。

    - 组命令，管道，命令替换会产生子Shell

        ```bash
        (echo "$SHLVL  $BASH_SUBSHELL")			# 在子Shell执行的组命令
        { command1; command2; command3; }		# 在当前Shell执行的组命令
        
        echo "test" | { echo "$SHLVL  $BASH_SUBSHELL"; }	# 管道
        
        var=$(echo "$SHLVL  $BASH_SUBSHELL")	# 命令替换
        echo $var
        ```

- 子进程

    - 使用 fork() 和 exec() 函数，即通过 fork() 创建子进程后立即调用 exec() 函数加载新的可执行文件，不使用父进程的参数。

    - 下列几种方式会创建子进程，运行结束后立即退出子进程

        ```bash
        # a_script.sh
        bash ./test.sh
        ./test.sh
        chmod +x ./test.sh
        ```

- `&` 启动并行进程，加速命令执行

    ```bash
    PIDARRAY=()
    for file in f1 f2
    do 
        echo $file &		# 命令符&, 将命令置于后台并继续执行脚本
        PIDARRAY+=("$!")	# $! 保存着最近一个后台进程的PID
    done
    wait ${PIDARRAY[@]}		# wait 命令等待这些进程结束
    ```



## Commands

- `printf`
    - 格式化输出

        ```shell
        > printf "ID: %-5s NAME: %-10s Avage: %4.2f\n" 1 tom 74.233;
        ID: 1     NAME: tom        Avage: 74.23
        ```

    - 输出重复字符串

        ```bash
        printf 'hello %.0s' {1..6}
        ```

    - `--`  means whatever follows should **not** be interpreted as a command line *option* to `printf`

        ```bash
        printf -- '--- hello ---'
        ```

- `xargs`: 传递参数的过滤器

    - 列出当前文件夹下每个pdf文件的大小

        ```bash
        ls *.pdf | xargs -n1 -I{} du -h {} 
        # -n1：每行输出一个
        # -I{}：substitute occurrences of {} in command with the parsed argument
        ```

## Others

- Shell [语法](https://segmentfault.com/a/1190000008080537), #TODO

## [后台运行](./shell脚本后台运行)

