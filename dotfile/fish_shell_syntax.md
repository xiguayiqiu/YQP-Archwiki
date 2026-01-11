**[返回](Shell_script.md)**

# 面向新手的fish完整教程：从入门到实用

# 前言：新手必知的fish基础认知

## 1.1 什么是fish？

fish（Friendly Interactive Shell）是一款面向新手的现代化shell，核心优势是“友好交互”与“简洁语法”。它默认提供自动补全、语法高亮、命令历史搜索等实用功能，无需复杂配置即可上手，区别于bash的繁琐语法，更适合终端新手快速入门，也受资深用户青睐用于日常终端操作。
- 核心特点：语法简洁（无需记忆复杂符号）、默认友好交互（自动补全/高亮）、内置帮助系统、跨平台兼容（Linux/macOS/Windows WSL）；
- 适用场景：日常终端操作、简单脚本编写、追求高效交互体验的用户，尤其适合从图形界面过渡到终端的新手。

## 1.2 如何安装与切换fish？

fish非系统默认shell，需手动安装，新手按以下步骤操作（跨平台通用）：

```shell
# 1. 安装fish（按系统选择命令）
# macOS（使用brew，需先安装brew：/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)")
brew install fish

# Linux（Ubuntu/Debian）
sudo apt update && sudo apt install fish

# Linux（CentOS/Fedora）
sudo dnf install fish

# 2. 验证安装：查看版本
fish --version  # 输出类似：fish, version 3.6.1

# 3. 切换到fish
fish  # 终端提示符变为 >，表示切换成功

# 4. 永久设置为默认shell（可选，新手建议先试用再设置）
chsh -s $(which fish)  # 重启终端生效
# 恢复默认shell（若需切换回bash）：chsh -s /bin/bash
```

新手提示：首次使用建议先通过 `fish` 命令临时切换试用，熟悉后再设置为默认shell，避免因不适应影响日常使用。

## 1.3 fish默认友好功能（新手直接享）

fish无需配置，默认提供以下提升效率的功能，新手可直接体验：
1. 语法高亮：输入命令时，正确命令显绿色，错误命令显红色，避免输错；
2. 智能自动补全：输入命令/路径/文件名时，按Tab键自动补全，支持模糊匹配；
3. 命令历史搜索：按Ctrl+R，输入关键词即可搜索历史命令（无需记忆 ! 符号）；
4. 自动建议：输入命令时，终端会显示灰色的历史命令建议，按右箭头 → 即可采纳。

# 第二章：fish核心语法（新手必学，简洁易懂）

fish的语法比bash简洁很多，去掉了bash中繁琐的符号（如 $()、[]、分号等），核心逻辑更贴近自然语言，新手重点掌握“变量、条件判断、循环、命令替换”四大模块即可覆盖日常需求。

## 2.1 变量：给数据起个“名字”（fish最简语法）

fish的变量定义和使用极其简单，无需加 $ 符号（仅在特殊场景需引用时加），是新手最容易上手的部分。

### 2.1.1 变量的定义规则
- 变量名由字母、数字、下划线组成，不能以数字开头（如 name 合法，1name 非法）；
- 定义时等号 = 两边无空格（和bash一致，新手易踩坑点）；
- 无需声明类型，直接赋值即可（字符串、数字通用）；
- 使用变量时，直接写变量名（无需加 $），仅在字符串中引用时需加 $。

### 2.1.2 变量的基础使用（示例实操）

```fish
# 1. 定义变量（简洁！无需任何多余符号）
set name "fish新手"  # 字符串变量（含空格必须加引号）
set age 20           # 数字变量
set address "北京市 朝阳区"  # 含空格的字符串变量

# 2. 使用变量（直接写变量名，无需 $）
echo 我的名字：name        # 输出：我的名字：fish新手
echo 我的年龄：age 岁      # 输出：我的年龄：20 岁
echo 我的地址：address      # 输出：我的地址：北京市 朝阳区

# 3. 字符串中引用变量（需加 $）
echo "我是$name，今年$age岁"  # 输出：我是fish新手，今年20岁

# 4. 修改变量值（重新赋值即可）
set age 21
echo 修改后的年龄：age  # 输出：修改后的年龄：21

# 5. 删除变量
set -e age  # -e 表示删除（erase）
echo 删除后的年龄：age  # 无输出（变量已删除）
```

### 2.1.3 fish变量扩展（新手常用功能）

fish支持基础的变量扩展，用于默认值设置、字符串替换、路径处理，语法比bash更简洁，新手重点掌握3种：

```fish
# 1. 设置默认值：变量未定义/为空时，显示默认内容（格式：$变量名[=默认值]）
echo 我的性别：$gender[=未知]  # gender未定义，输出：我的性别：未知
set gender "男"
echo 我的性别：$gender[=未知]  # gender已定义，输出：我的性别：男

# 2. 字符串替换：替换变量中所有匹配的字符（格式：$变量名[//旧字符/新字符]）
set str "我喜欢fish，fish很友好"
echo $str[//fish/shell]  # 替换所有fish为shell，输出：我喜欢shell，shell很友好

# 3. 路径处理：获取文件名/目录路径（fish内置功能，无需复杂符号）
set file_path "$HOME/Desktop/note.txt.bak"
echo 完整路径：$file_path  # 输出：/home/xxx/Desktop/note.txt.bak
echo 文件名：(basename $file_path)  # 获取文件名，输出：note.txt.bak
echo 目录路径：(dirname $file_path)  # 获取目录路径，输出：/home/xxx/Desktop
```

### 2.1.4 fish系统内置变量（新手直接用）

fish内置了常用系统变量，无需定义即可使用，新手记以下5个核心变量：

```fish
echo 当前fish版本：$FISH_VERSION  # 输出fish版本，如 3.6.1
echo 当前登录用户：$USER           # 输出用户名，如 xxx
echo 当前所在目录：$PWD            # 输出当前终端目录路径
echo 命令历史记录文件：$HISTFILE   # 输出历史命令保存路径
echo 当前进程ID：$fish_pid         # 输出当前fish进程的ID号
```

新手注意：fish的变量名区分大小写！$USER（大写）是内置变量，$user（小写）是自定义变量，不要混淆。

## 2.2 条件判断：满足条件才执行命令（fish自然语法）

fish的条件判断语法比bash简洁太多，去掉了 bash 中繁琐的 [ ] 或 (( ))，直接用 `if ...; then ...; end` 结构，条件表达式更贴近自然语言。

### 2.2.1 基础语法结构

```fish
# 单分支：满足条件执行
if 条件表达式
    要执行的命令
end

# 双分支：满足条件执行A，不满足执行B
if 条件表达式
    命令A
else
    命令B
end

# 多分支：多个条件依次判断
if 条件表达式1
    命令1
else if 条件表达式2  # fish用 else if，不是 bash 的 elif！
    命令2
else
    命令3
end
```

新手提示：fish的条件判断不需要分号分隔（除了 then 前可选），结构更清晰；注意多分支用 `else if`（两个单词），和bash的 `elif` 区分开。

### 2.2.2 3类核心条件表达式（新手必记）

fish支持文件判断、字符串判断、数值判断3类常用场景，语法比bash更直观，整理为表格方便记忆：

|判断类型|表达式写法（fish）|含义|示例|
|---|---|---|---|
|文件判断|test -f $文件|判断是否为普通文件（非目录、链接）|test -f note.txt|
|test -d $目录|判断是否为目录|test -d Desktop|
|test -e $路径|判断路径是否存在（文件/目录均可）|test -e ~/Documents|
|字符串判断|test "$str1" = "$str2"|判断两个字符串是否相等（等号前后加空格）|test "fish" = "shell"|
|test -z $str|判断字符串是否为空|test -z $empty_str|
|test -n $str|判断字符串是否非空|test -n "$name"|
|数值判断|test $a -eq $b|a等于b（eq=equal）|test 10 -eq 20|
|test $a -gt $b|a大于b（gt=greater than）|test 20 -gt 10|
|test $a -lt $b|a小于b（lt=less than）|test 10 -lt 20|
|test $a -ge $b|a大于等于b（ge=greater or equal）|test 20 -ge 20|

### 2.2.3 新手实战示例（fish专属）

#### 示例1：文件存在性判断（日常最常用）

```fish
# 需求：判断桌面的note.txt是否存在，存在则查看内容，不存在则创建
set file "$HOME/Desktop/note.txt"  # $HOME等价于用户主目录（~）

if test -f "$file"
    echo "文件存在，正在查看内容..."
    cat "$file"  # 查看文件内容
else
    echo "文件不存在，正在创建..."
    touch "$file"  # 创建文件
    echo "这是fish新手的笔记文件" > "$file"  # 写入内容
end
```

#### 示例2：数值比较（判断考试分数是否及格）

```fish
# 需求：定义分数，判断是否及格（60分及以上）
set score 75

# fish数值比较：直接用 test 命令，语法直观
if test $score -ge 60
    echo "恭喜！分数 $score，及格了～"
else
    echo "加油！分数 $score，未及格。"
end
```

## 2.3 循环：重复执行同一组命令（fish简洁语法）

fish支持 `for` 循环（遍历场景）和 `while` 循环（条件循环场景），语法比bash更简洁，去掉了 do 和分号，新手容易掌握。

### 2.3.1 for循环：遍历列表/文件（最常用）

语法：`for 变量名 in 遍历列表; 命令; end`（fish可省略 do，分号可选，用换行分隔更清晰）

#### 示例1：遍历固定列表

```fish
# 需求：遍历3种shell名称，依次打印
for shell in fish bash sh
    echo "当前遍历的shell：$shell"
end

# 执行结果：
# 当前遍历的shell：fish
# 当前遍历的shell：bash
# 当前遍历的shell：sh
```

#### 示例2：遍历当前目录的txt文件

```fish
# 需求：遍历当前目录所有.txt文件，打印文件名和行数
for file in *.txt
    # 关键：判断是否为有效文件（避免无txt时，*.txt被当作字符串）
    if test -f "$file"
        set line_count (wc -l < "$file")  # 统计行数（fish命令替换语法）
        echo "$file 的行数：$line_count"
    end
end
```

### 2.3.2 while循环：满足条件持续执行

语法：`while 条件表达式; 命令; end`（条件为真时重复执行，需确保条件能终止）

#### 示例1：计数循环（打印1到5）

```fish
# 需求：从1数到5，每数一个打印一次
set count 1  # 初始化计数器

while test $count -le 5  # -le 表示小于等于
    echo "当前计数：$count"
    set count (math $count + 1)  # 计数器自增1（fish数值运算用 math 命令）
end
```

#### 示例2：逐行读取文件内容

```fish
# 需求：逐行读取note.txt的内容，打印每一行
set file "$HOME/Desktop/note.txt"

# 先判断文件是否存在
if not test -f "$file"  # not 表示否定，比bash的 ! 更直观
    echo "错误：文件 $file 不存在"
    exit 1  # 退出脚本，1表示错误码
end

# 逐行读取（fish标准写法）
while read -r line
    echo "文件内容：$line"
end < "$file"  # < $file 表示从文件读取内容，而非终端
```

新手注意：fish的数值运算需用 `math` 命令（如 `math 10 + 20`），或直接用 `set count (expr $count + 1)`，不能用bash的 `$((count + 1))` 语法。

## 2.4 命令替换：把命令结果存到变量（fish最简语法）

fish的命令替换极其简洁，直接用 `(命令)` 包裹命令即可（去掉了bash的 $ 符号），无需记忆反引号或复杂格式，新手轻松掌握。

### 2.4.1 基础示例

```fish
# 示例1：获取当前目录路径，存入变量
set current_dir (pwd)
echo "当前目录：$current_dir"

# 示例2：统计文件行数，直接打印
echo "note.txt 的行数："(wc -l < "$HOME/Desktop/note.txt")

# 示例3：嵌套使用（获取当前目录txt文件个数）
set txt_count (ls -l *.txt | wc -l)
echo "当前目录txt文件个数：$txt_count"
```

### 2.4.2 新手实用场景：获取系统信息

```fish
# 需求：获取当前系统CPU核心数、内存使用情况（fish通用）
# 获取CPU核心数（Linux）
set cpu_core (grep -c ^processor /proc/cpuinfo)
# 获取CPU核心数（macOS）
# set cpu_core (sysctl -n hw.ncpu)

# 获取内存使用情况（Linux）
set mem_usage (free -h | grep Mem | awk '{print $3 "/" $2}')
# 获取内存使用情况（macOS）
# set mem_usage (top -l 1 -s 0 | grep PhysMem | awk '{print $2 "/" $4}')

echo "CPU核心数：$cpu_core"
echo "内存使用情况：$mem_usage"
```

# 第三章：fish函数教程：封装常用命令（提升效率核心）

fish函数用于将“重复执行的一组命令”封装成自定义指令，语法比bash更简洁，无需担心括号、分号的繁琐格式，新手可快速封装常用功能（如快速跳转、文件统计等）。

## 3.1 函数的基础定义与调用

### 3.1.1 fish函数定义格式（唯一简洁写法）

```fish
# fish函数定义：函数名 括号可选，直接写命令体
function 函数名
    # 函数体：要封装的命令集合
    命令1
    命令2
end

# 注意：fish函数无需加 ()，也无需分号，结构极其清晰！
```

### 3.1.2 第一个fish函数：快速跳转到桌面

```fish
# 定义函数：cdDesk，功能：跳转到桌面并查看文件列表
function cdDesk
    set desk_dir "$HOME/Desktop"  # 局部变量（fish函数内变量默认局部！）
    if test -d "$desk_dir"
        cd "$desk_dir"  # 跳转到桌面
        ls -l  # 以列表形式查看文件（详细信息）
    else
        echo "错误：桌面目录 $desk_dir 不存在"
    end
end

# 调用函数：直接输入函数名（无需加括号）
cdDesk
```

执行效果：终端自动跳转到桌面，并显示所有文件的详细信息（权限、大小、修改时间等）。

fish新手福利：fish函数内的变量默认是局部变量，无需像bash那样加 local 关键字！彻底避免全局变量污染问题，新手无需担心踩坑。

## 3.2 带参数的fish函数：灵活适配多场景

fish函数的参数无需声明，直接通过 `$argv` 数组获取（$argv[1] 是第一个参数，$argv[2] 是第二个参数...），比bash的 $1、$2 更直观，新手容易记忆。

### 核心参数相关变量（新手必记）

|变量|含义|示例（调用：func a b c）|
|---|---|---|
|$argv[1]|第一个参数|$argv[1] = "a"|
|$argv[2]|第二个参数|$argv[2] = "b"|
|$argv|所有参数（数组）|$argv = ["a", "b", "c"]|
|$argv_count|参数总个数|$argv_count = 3|

### 3.2.1 实战示例1：查看指定文件的行数

```fish
# 定义函数：fileLine，参数1为文件名，功能：查看文件行数
function fileLine
    # 第一步：检查参数个数（必须传1个参数）
    if test $argv_count -ne 1
        echo "用法错误！正确用法：$0 文件名"
        echo "示例：$0 note.txt"
        return 1  # 退出函数，1表示错误
    end

    # 第二步：检查文件是否存在
    set file "$argv[1]"  # 获取第一个参数（文件名）
    if not test -f "$file"
        echo "错误：文件 $file 不存在"
        return 1
    end

    # 第三步：统计行数并输出
    set line_count (wc -l < "$file")
    echo "$file 的行数：$line_count"
    return 0  # 0表示执行成功
end

# 调用示例
fileLine note.txt  # 正确，输出：note.txt 的行数：xxx
fileLine          # 错误，输出用法提示
```

### 3.2.2 实战示例2：创建带名称的笔记文件

```fish
# 定义函数：newNote，参数1为笔记名称，功能：创建.md笔记并打开
function newNote
    # 检查参数个数
    if test $argv_count -ne 1
        echo "用法错误！正确用法：$0 笔记名称"
        echo "示例：$0 今日学习笔记"
        return 1
    end

    # 定义笔记路径（桌面目录）
    set note_name "$argv[1]"
    set note_path "$HOME/Desktop/$note_name.md"

    # 检查文件是否已存在（避免覆盖）
    if test -f "$note_path"
        echo "错误：笔记 $note_path 已存在"
        return 1
    end

    # 创建文件并写入标题
    touch "$note_path"
    echo "# $note_name" > "$note_path"  # 写入Markdown标题
    echo "创建成功！笔记路径：$note_path"

    # 打开文件（系统兼容写法）
    if test (uname) = "Darwin"
        open "$note_path"  # macOS打开方式
    else if test (uname) = "Linux"
        xdg-open "$note_path"  # Linux打开方式
    end
end

# 调用示例
newNote 今日学习笔记  # 桌面创建“今日学习笔记.md”并打开
```

## 3.3 fish函数的永久保存：关掉终端也能使用

直接在终端定义的fish函数，关掉终端后会消失。新手需将常用函数保存到fish的配置文件 `~/.config/fish/config.fish` 中，实现永久生效（fish的配置文件路径固定，比bash更统一）。

### 具体步骤（新手一步一步来）
1. 打开配置文件： 终端输入 `vim ~/.config/fish/config.fish`（用vim编辑） 新手也可用文本编辑器打开： macOS：`open ~/.config/fish/config.fish` Linux：`xdg-open ~/.config/fish/config.fish` （若 ~/.config/fish 目录不存在，先创建：`mkdir -p ~/.config/fish`）
2. 添加函数：在文件末尾粘贴写好的函数（如 cdDesk、fileLine）；
3. 保存文件： vim编辑器：按 Esc 键，输入 `:wq`（w=保存，q=退出），回车； 文本编辑器：直接点击“保存”；
4. 让配置生效：无需重启终端，直接输入以下命令 `source ~/.config/fish/config.fish`

验证：关掉当前终端，重新打开，输入 `cdDesk`，若能跳转到桌面，说明函数已永久生效。

## 3.4 新手实用fish函数合集（直接抄）

```fish
# 1. 快速跳转到工作目录（替换为你的工作目录）
function cdWork
    set work_dir "$HOME/Documents/工作"
    if test -d "$work_dir"
        cd "$work_dir"
        ls -l
    else
        echo "错误：工作目录 $work_dir 不存在"
    end
end

# 2. 批量重命名：当前目录txt文件→md文件
function renameTxtToMd
    # 检查是否有txt文件
    set txt_files *.txt
    if not test -f "$txt_files[1]"  # 判断第一个文件是否存在
        echo "当前目录没有.txt文件"
        return 1
    end

    # 批量重命名
    for file in *.txt
        set new_file (echo "$file" | sed 's/\.txt/\.md/')  # 替换后缀
        mv "$file" "$new_file"
        echo "重命名：$file → $new_file"
    end
end

# 3. 查看指定端口占用（如 8080 端口）
function checkPort
    if test $argv_count -ne 1
        echo "用法错误！正确用法：$0 端口号"
        echo "示例：$0 8080"
        return 1
    end

    set port "$argv[1]"
    # lsof查看端口占用，2>/dev/null 忽略错误输出
    lsof -i :"$port" 2>/dev/null || echo "$port 端口未被占用"
end

# 4. 快速查看fish帮助（新手必备）
function fishHelp
    echo "fish新手常用命令帮助："
    echo "1. 定义变量：set 变量名 值（如 set name 张三）"
    echo "2. 条件判断：if test 条件; 命令; end"
    echo "3. for循环：for 变量 in 列表; 命令; end"
    echo "4. 命令替换：(命令)（如 set dir (pwd)）"
    echo "5. 查看函数：functions 函数名（如 functions cdDesk）"
end
```

# 第四章：新手实操练习（巩固fish知识点）

以下练习覆盖fish核心语法和函数，新手建议逐题手动实操，加深理解：

## 练习1：变量与变量扩展

需求：定义变量 `set file_path "$HOME/Desktop/test.txt"`，完成3个操作：
1. 打印变量值，格式：“文件路径：xxx”；
2. 用变量扩展获取文件名（test.txt），打印：“文件名：xxx”；
3. 用变量扩展获取目录路径（$HOME/Desktop），打印：“目录路径：xxx”。

## 练习2：条件判断

需求：判断 `$HOME/Desktop/test.txt` 是否存在，存在则打印行数，不存在则创建文件并写入“fish新手测试文件”。

## 练习3：循环

需求：遍历 `$HOME/Desktop` 目录，区分“普通文件”和“目录”，分别打印：“文件：xxx”“目录：xxx”。

## 练习4：函数

需求：定义函数 `delEmptyFile`，功能：删除当前目录下所有空文件（大小为0），要求：
1. 先检查是否有空文件；
2. 删除前打印要删除的文件名；
3. 删除完成后，打印“共删除x个空文件”。

# 第五章：新手常见问题与解决方案（fish专属）

## 问题1：切换到fish后，bash的函数/别名不能用

原因：fish的语法和bash不兼容，bash的函数、别名无法直接在fish中使用。

解决方案：1. 将bash函数按fish语法重新改写，保存到 `~/.config/fish/config.fish`；2. 别名用fish的 `alias` 命令定义（如 `alias ll='ls -l'`），并保存到配置文件。

## 问题2：函数调用提示“command not found”

原因：1. 函数名拼写错误；2. 函数未保存到 `~/.config/fish/config.fish`；3. 未执行`source` 让配置生效。

解决方案：1. 核对函数名拼写；2. 重新执行 `source ~/.config/fish/config.fish`；3. 检查函数定义语法（如 `function` 和 `end` 是否配对）。

## 问题3：数值运算报错“invalid integer”

原因：使用了bash的数值运算语法（如 `$((count + 1))`），fish不支持。

解决方案：用fish的 `math` 命令进行数值运算，如 `set count (math $count + 1)`。

## 问题4：循环遍历文件时，出现“*.txt: No such file or directory”

原因：目录下无.txt文件，fish将通配符 `*.txt` 当作普通字符串处理。

解决方案：在循环内添加文件判断：`if test -f "$file"; 命令; end`，跳过非文件内容。