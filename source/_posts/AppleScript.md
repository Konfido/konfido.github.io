---
title: AppleScript
tags: [Handbook]
date: 2019-01-12 22:08:07
updated: 2020-08-30 13:55:02
blogged: true
urlname: AppleScript
---



## Basic

- 运行：`osascript foo.scpt`

- 注释：

    - 行注释，可用在行尾：`--` or `#`
    - 块注释：`(* *)`

- 数据类型：number，string，list，record (字典)

    - 设置list

        ```objc
        set L to {1,2,3,"ss"}
        set item 3 of L to "xx"
        ```

    - 转换：`set n2s to "123" as number`

- Keywords

    - `me` refers to the current script
        - `my` is synonym for `of me`
    - `it` refers to the current target (A `tell` statement specifies a default target)
        - `its` is synonym for `of it`

### 流程控制

- 条件语句

    ```objc
    if a=0 then
        xxx
    else if a=1 then
        xxx
    else
        xxx
    end if
    ```

- 循环语句

    - `times`
      
      ```objc
      repeat 10 times
          xxx
      end repeat
      ```
      
    - `from .. to ..`
      
      ```objc
      repeat with n from 0 to 10 by 1
          xxx
      end repeat
      ```
      
    - `until`
      
      ```objc
      repeat until n > 10
          xxx
          set n to n +3
      end repeat
      ```

- `try`

    ```objc
    try
        -- Do something
    on error
        -- other thing
    end try
    ```

### Function/Handler

- `on` or `to`

    ```objc
    on foo()
        display dialog "Error!"
        return "xx"
    end foo
        
    foo()
    set R to foo()
    ```

- 带参数

    ```objc
    to foo(theErrorMessage, theButtons)
        display dialog the theErrorMessage buttons theButtons
    end displayError
        
    foo("xxx",{"ok","cancel"})
    ```

- 交叉参数

    ```objc
    on foo:E withButtons:B
        display dialog E buttons B
    end foo:withButtons:
    ```



### 交互

- 发声

    - `say "hello world" using "Victoria"`
    - `beep 2`

- dialog 弹窗：

    - `display dialog "xxx"`

    - 获取输入

        ```objc
        display dialog "xxx" default answer ""
        set a to text returned of result
        ```

    - 获取点击项

        ```objc
        set message to "Hello world!"
        set DWindows to display dialog message buttons {"OK", "Cancel"}
        set selectedResult to button returned of DWindows
        Say selectedResult
        ```

- notification

    ```objc
    display notification "..." with title "xxx" subtitle "hhh"
    ```

- 列表选择

    ```objc
    choose from list {"aa","bb","cc"} with title "Names" ¬
        with prompt "请选择：" default items {"bb"} ¬
        with empty selection allowed and multiple selections allowed
    ```

- 文件/夹选择

    - `choose file of type("txt") with prompt "请选择" `
    - `choose folder`

    

###  模拟行为

- 唤起应用至frontmost：`tell application "Chrome" to activate`

- 延时/s：`delay 2`

- 模型点击：`click`

    - 位置
      
        ```objective-c
        tell application "System Events"
            click at {123,456}
        end tell
        ```
        
    - 元素
      
        ```objective-c
        tell application "System Events"
            tell menu bar 1 of process "Chrome"
            	click menu item "新标签页" of menu "文件" of menu bar item "文件"
            end tell
        end tell
        ```

- 键盘输入

    - `key code 48 using command down `：`⌘Tab`
      
        - List of AppleScript [key codes](https://eastmanreference.com/complete-list-of-applescript-key-codes)
    - `keystroke "hello"`

        

- 剪贴板：`clipboard`

    - `set c to get the clipboard`
    - `set the clipboard to "xxx"`



## Advanced

### AppleScript中执行Shell指令

- Basic

    ```objective-c
    do shell script "date +'%T'"
    ```

- ==传递变量==

    ```objective-c
    set foo to "test"
    do shell script "echo " & foo
    ```

    ```objective-c
    do shell script "echo " & quoted form of foo
    ```

### Shell 中执行 AppleScript

1. 两种方法

    1. `osascript -e "echo \"Hello\"`

    2. 命令语句较多时

        ```shell
        osascript <<EOF
            say "Hello"
            say "World"
        EOF
        ```

2. 传递shell中的变量

    1. `osascript -e "echo \"$foo\" "`

    2. 命令语句较多时

        ```shell
        osascript <<EOF
        set a to "Hello"
        set b to "$foo"		# 传递shell中的变量
        EOF
        ```

3. Shell 获取 Applescript 结果

    ```bash
    finder=$(osascript -e "set dir to POSIX path of (choose folder)")
    ```

    

## Others

### Tips

- `AppleScript's Dictionary`: Press `⌘⇧O` to open AppleScript dictionary of this app.

- `Watch Me Do`（"我做给您看"）
  
    - 点击AutoMator的录制按钮（红色小圆点）
    - 实际演示一遍想要完成的操作后，结束录制
- 将自动生成的动作序列拖到下方空白处，可以看到转成的AppleScript细节（uiScript）
  
- `Accessibility Inspector`

    - Check the UI element tree, properties, attribute ...

      ```objc
      return attribute of textArea
      return the value of attribute "AXValue" of xxx
      ```

      ```objc
      get properties of front window
      # {.., name:"foo", description: "xxx", ...}
      get name of front window
      # "foo"
      ```

- 语句过长时，可以使用符号 `¬` 断开

    ```objc
    tell process "xxxxx" to set A to a reference ¬
        to (first window whose name of attributes contains ".txt")
    ```

- 使用语句`log variable`进行设置断点进行debug



### 应用

- toggle dock's preferences of "autohide"

    ```objc
    tell application "System Events"
        tell dock preferences
            if autohide is true then
                set properties to {autohide:false}
            else
                set properties to {autohide:true}
            end if
        end tell
    end tell
    ```


### Exaples

- [https://github.com/unforswearing/applescript](https://github.com/unforswearing/applescript)

    

### Documentation

- AppleScript Language [Guide](https://developer.apple.com/library/archive/documentation/AppleScript/Conceptual/AppleScriptLangGuide/introduction/ASLR_intro.html#/)

- Mac Automation Scripting [Guide](https://developer.apple.com/library/archive/documentation/LanguagesUtilities/Conceptual/MacAutomationScriptingGuide/index.html#//apple_ref/doc/uid/TP40016239-CH56-SW1)

