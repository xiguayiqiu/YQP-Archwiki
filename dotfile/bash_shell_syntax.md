**[返回](Shell_script.md)**


# 前言：新手必知的bash基础认知

## 1.1 什么是bash？

bash是大多数Linux系统、macOS（旧版本默认）的默认shell（命令行解释器），负责接收并执行用户输入的命令。它语法规范、兼容性强，是shell的“基础标准”，也是新手入门终端的首选工具。
- bash：语法稳定、兼容性广，几乎所有类Unix系统都预装，是学习shell的基础；
- 核心优势：命令简洁、功能全面，支持变量、条件判断、循环、函数等核心特性，满足日常终端操作和脚本编写需求。

新手友好提示：bash是shell的基础标准，掌握bash的核心语法后，可轻松适配其他shell工具。本教程专注讲解bash的基础用法和实用技巧，帮助新手快速上手。

## 1.2 如何使用bash？
1. 查看当前shell：终端输入 `echo $SHELL`，输出 `/bin/bash` 为bash，`/bin/zsh` 为zsh；
2. 验证bash是否可用：大多数系统默认预装bash，输入 `bash --version` 可查看版本；若未安装（极少数情况）：Linux输入 `sudo apt install bash` 安装；
3. 验证切换成功：重新执行 `echo $SHELL` 查看输出。

# 第二章：bash核心语法（新手必学）

核心语法是bash使用的基础，本节按“变量→条件判断→循环→命令替换”的顺序讲解，每个知识点包含“语法规则+示例+注意事项”，方便新手理解和实操。

## 2.1 变量：给数据起个“名字”

变量用于存储字符串、数字等数据，是bash中最基础的元素，掌握变量的定义和使用是后续学习的基础。

### 2.1.1 变量的定义规则
- 变量名由字母、数字、下划线组成，不能以数字开头（如`name`合法，`1name` 非法）；
- 定义时 `=` 两边必须无空格（新手最易踩坑！）；
- 变量无需声明类型（字符串、数字直接赋值即可）。

### 2.1.2 变量的基础使用（定义+调用）

示例（直接在终端敲，实时看效果）：

```bash
# 1. 定义变量
name="bash新手"  # 字符串变量（含空格必须加引号）
age=20          # 数字变量
address="北京市 朝阳区"  # 含空格的字符串

# 2. 调用变量：变量名前加 $，推荐用 ${变量名} 避免歧义
echo "我的名字：$name"        # 基础用法，输出：我的名字：bash新手
echo "我的年龄：${age}岁"     # 推荐用法，避免和后续字符混淆
echo "我的地址：${address}"   # 含空格的变量必须用 ${} 调用
```

### 2.1.3 bash变量扩展（新手常用基础功能）

bash支持基础的变量扩展功能，可实现默认值设置、字符替换、后缀/前缀删除，是提升变量使用效率的关键，新手重点掌握以下3种：

```bash
# 1. 设置默认值：变量未定义/为空时，显示默认内容（格式：${变量名:-默认值}）
echo "我的性别：${gender:-未知}"  # gender未定义，输出：我的性别：未知
gender="男"
echo "我的性别：${gender:-未知}"  # gender已定义，输出：我的性别：男

# 2. 字符替换：替换变量中第一个匹配的字符（格式：${变量名/旧字符/新字符}）
str="我喜欢bash"
echo "${str/bash/shell}"  # 替换第一个bash为shell，输出：我喜欢shell

# 3. 后缀/前缀删除：删除变量末尾/开头的指定字符（新手常用：处理文件名）
file_name="note.txt.bak"
echo "${file_name%.bak}"  # 删除末尾的.bak后缀，输出：note.txt
echo "${file_name#note.}"  # 删除开头的note.前缀，输出：txt.bak
```

### 2.1.4 bash系统内置变量（新手直接用）

bash内置了多个常用系统变量，无需定义即可直接调用，新手记以下5个核心变量：

```bash
echo "当前bash版本：${BASH_VERSION}"  # 输出bash版本，如 5.1.16(1)-release
echo "当前登录用户：${USER}"         # 输出用户名，如 xxx
echo "当前所在目录：${PWD}"          # 输出当前终端目录路径，如 /home/xxx/Desktop
echo "命令历史记录文件：${HISTFILE}"  # 输出历史命令保存路径，如 /home/xxx/.bash_history
echo "当前进程ID：${$$}"             # 输出当前bash进程的ID号
```

### 2.1.5 新手注意事项（变量相关）
1. 定义变量时，等号两边绝对不能有空格！错误写法：`name = "test"`，正确写法：`name="test"`；
2. 变量值含空格时，必须用单引号或双引号包裹，否则bash会把空格后的内容当作新命令；
3. 调用变量时，优先用 `${变量名}` 而非 `$变量名`，避免与后续字符混淆（如 `${age}岁` 不会被识别为 `$age岁`）。

## 2.2 条件判断：满足条件才执行命令

bash的条件判断用于实现分支逻辑，核心语法为 `[ ]`（基础通用）或 `[[ ]]`（兼容bash 4.0+），数值比较推荐用 `(( ))`，新手先掌握`[ ]` 的基础用法。

### 2.2.1 基础语法结构

```bash
# 单分支：满足条件执行
if [ 条件表达式 ]; then
    要执行的命令
fi

# 双分支：满足条件执行A，不满足执行B
if [ 条件表达式 ]; then
    命令A
else
    命令B
fi

# 多分支：多个条件依次判断
if [ 条件1 ]; then
    命令1
elif [ 条件2 ]; then
    命令2
else
    命令3
fi
```

新手提示：`if` 与 `fi` 必须成对出现（fi是if的反向拼写）；`[ ]` 内部的条件表达式前后必须加空格（如 `[ -f test.txt ]` 正确，`[-f test.txt]` 错误）。

### 2.2.2 3类核心条件表达式（新手必记）

条件表达式是判断的核心，bash支持文件判断、字符串判断、数值判断3类常用场景，整理为表格方便记忆：

|判断类型|表达式写法|含义|示例（bash通用）|
|---|---|---|---|
|文件判断|-f $文件|判断是否为普通文件（非目录、链接）|[ -f note.txt ]|
|-d $目录|判断是否为目录|[ -d Desktop ]|
|-e $路径|判断路径是否存在（文件/目录均可）|[ -e ~/Documents ]|
|字符串判断|"$str1" = "$str2"|判断两个字符串是否相等（等号前后加空格）|[ "bash" = "shell" ]|
|-z $str|判断字符串是否为空|[ -z $empty_str ]|
|-n $str|判断字符串是否非空|[ -n "$name" ]|
|数值判断|$a -eq $b|a等于b（eq=equal）|[ 10 -eq 20 ] 或 (( 10 == 20 ))|
|$a -gt $b|a大于b（gt=greater than）|[ 20 -gt 10 ] 或 (( 20 > 10 ))|
|$a -lt $b|a小于b（lt=less than）|[ 10 -lt 20 ] 或 (( 10 < 20 ))|
|$a -ge $b|a大于等于b（ge=greater or equal）|[ 20 -ge 20 ] 或 (( 20 >= 20 ))|

### 2.2.3 新手实战示例（bash专属）

#### 示例1：文件存在性判断（日常最常用）

```bash
# 需求：判断桌面的note.txt是否存在，存在则查看内容，不存在则创建
file="$HOME/Desktop/note.txt"  # $HOME等价于用户主目录（~）

if [ -f "$file" ]; then
    echo "文件存在，正在查看内容..."
    cat "$file"  # 查看文件内容
else
    echo "文件不存在，正在创建..."
    touch "$file"  # 创建文件
    echo "这是bash新手的笔记文件" > "$file"  # 写入内容
fi
```

#### 示例2：数值比较（判断考试分数是否及格）

```bash
# 需求：定义分数，判断是否及格（60分及以上）
score=75

# bash数值比较两种写法：[ ] 用 -ge，(( )) 用 >=（更简洁）
if (( score >= 60 )); then
    echo "恭喜！分数 $score，及格了～"
else
    echo "加油！分数 $score，未及格。"
fi
```

## 2.3 循环：重复执行同一组命令

bash的循环用于重复执行命令，新手重点掌握 `for` 循环（遍历场景）和 `while` 循环（条件循环场景），两种循环覆盖日常90%以上的使用需求。

### 2.3.1 for循环：遍历列表/文件（最常用）

语法：`for 变量名 in 遍历列表; do 命令; done`，bash支持列表遍历、文件通配符遍历两种核心场景。

#### 示例1：遍历固定列表

```bash
# 需求：遍历3种shell名称，依次打印
for shell in bash sh ksh; do
    echo "当前遍历的shell：$shell"
done

# 执行结果：
# 当前遍历的shell：bash
# 当前遍历的shell：sh
# 当前遍历的shell：ksh
```

#### 示例2：遍历当前目录的txt文件

```bash
# 需求：遍历当前目录所有.txt文件，打印文件名和行数
for file in *.txt; do
    # 关键：判断是否为有效文件（避免无txt时，*.txt被当作字符串）
    if [ -f "$file" ]; then
        line_count=$(wc -l < "$file")  # 统计行数（< 重定向输入）
        echo "$file 的行数：$line_count"
    fi
done
```

### 2.3.2 while循环：满足条件持续执行

语法：`while [ 条件表达式 ]; do 命令; done`，条件为真时重复执行，需确保条件能终止（避免无限循环）。

#### 示例1：计数循环（打印1到5）

```bash
# 需求：从1数到5，每数一个打印一次
count=1  # 初始化计数器

while [ $count -le 5 ]; do  # -le 表示小于等于
    echo "当前计数：$count"
    count=$((count + 1))  # 计数器自增1（bash数值运算写法）
done
```

#### 示例2：逐行读取文件内容

```bash
# 需求：逐行读取note.txt的内容，打印每一行
file="$HOME/Desktop/note.txt"

# 先判断文件是否存在
if [ ! -f "$file" ]; then
    echo "错误：文件 $file 不存在"
    exit 1  # 退出脚本，1表示错误码
fi

# 逐行读取（bash标准写法）
while read -r line; do  # -r 避免转义字符被解析
    echo "文件内容：$line"
done < "$file"  # < $file 表示从文件读取内容，而非终端
```

### 2.3.3 新手注意事项（循环相关）
1. 遍历文件时，必须加 `[ -f "$file" ]` 判断，否则目录下无对应文件时，通配符（如 *.txt）会被当作普通字符串处理；
2. while循环需确保计数器能自增（如 `count=$((count + 1))`），否则会陷入无限循环（按 Ctrl+C 可强制终止）；
3. 逐行读取文件时，推荐加 `-r` 参数（`read -r line`），避免文件中的转义字符（如 \n）被错误解析。

## 2.4 命令替换：把命令结果存到变量

bash命令替换用于将“命令执行结果”作为值存入变量或直接使用，核心格式有两种：`$(命令)`（推荐，易读、支持嵌套）和 ``（反引号，兼容旧版本）。

### 2.4.1 基础示例

```bash
# 示例1：获取当前目录路径，存入变量
current_dir=$(pwd)
echo "当前目录：$current_dir"

# 示例2：统计文件行数，直接打印
echo "note.txt 的行数：$(wc -l < "$HOME/Desktop/note.txt")"

# 示例3：嵌套使用（获取当前目录txt文件个数）
txt_count=$(ls -l *.txt | wc -l)
echo "当前目录txt文件个数：$txt_count"
```

### 2.4.2 新手实用场景：获取系统信息

```bash
# 需求：获取当前系统CPU核心数、内存使用情况（bash通用）
# 获取CPU核心数（Linux）
cpu_core=$(grep -c ^processor /proc/cpuinfo)
# 获取CPU核心数（macOS）
# cpu_core=$(sysctl -n hw.ncpu)

# 获取内存使用情况（Linux）
mem_usage=$(free -h | grep Mem | awk '{print $3 "/" $2}')
# 获取内存使用情况（macOS）
# mem_usage=$(top -l 1 -s 0 | grep PhysMem | awk '{print $2 "/" $4}')

echo "CPU核心数：$cpu_core"
echo "内存使用情况：$mem_usage"
```

# 第三章：bash函数教程：封装常用命令（提升效率核心）

bash函数用于将“重复执行的一组命令”封装成自定义指令，比如把“cd到桌面+ls查看”封装成 `cdDesk`，后续只需输入函数名即可执行，大幅节省时间。

## 3.1 函数的基础定义与调用

### 3.1.1 bash函数定义格式（两种通用写法）

```bash
# 格式1：简洁版（推荐，新手易记）
函数名() {
    # 函数体：要封装的命令集合
    命令1
    命令2
}

# 格式2：完整版（用function关键字，与格式1等价）
function 函数名 {
    命令1
    命令2
}
```

### 3.1.2 第一个bash函数：快速跳转到桌面

```bash
# 定义函数：cdDesk，功能：跳转到桌面并查看文件列表
cdDesk() {
    local desk_dir="$HOME/Desktop"  # 局部变量，仅函数内有效
    if [ -d "$desk_dir" ]; then
        cd "$desk_dir"  # 跳转到桌面
        ls -l  # 以列表形式查看文件（详细信息）
    else
        echo "错误：桌面目录 $desk_dir 不存在"
    fi
}

# 调用函数：直接输入函数名（无需加括号）
cdDesk
```

执行效果：终端自动跳转到桌面，并显示所有文件的详细信息（权限、大小、修改时间等）。

### 3.1.3 新手注意事项（函数基础）
- 函数名命名规则与变量一致：字母、数字、下划线组成，不能以数字开头；
- 函数体中目录路径推荐用绝对路径（如 `$HOME/Desktop`），避免切换目录后执行失败；
- 调用函数时，直接输入函数名即可，无需加括号（错误写法：`cdDesk()`，正确写法：`cdDesk`）。

## 3.2 带参数的bash函数：灵活适配多场景

bash函数的参数无需声明，直接通过固定符号获取，新手记以下4个核心符号即可满足日常需求：

|符号|含义|示例（调用：func a b c）|
|---|---|---|
|$1|第一个参数|$1 = "a"|
|$2|第二个参数|$2 = "b"|
|$#|参数总个数|$# = 3|
|$@|所有参数（保留空格，按原样传递）|$@ = "a" "b" "c"|

### 3.2.1 实战示例1：查看指定文件的行数

```bash
# 定义函数：fileLine，参数1为文件名，功能：查看文件行数
fileLine() {
    # 第一步：检查参数个数（必须传1个参数）
    if [ $# -ne 1 ]; then
        echo "用法错误！正确用法：$0 文件名"
        echo "示例：$0 note.txt"
        return 1  # 退出函数，1表示错误
    fi

    # 第二步：检查文件是否存在
    local file="$1"  # 局部变量，仅函数内有效
    if [ ! -f "$file" ]; then
        echo "错误：文件 $file 不存在"
        return 1
    fi

    # 第三步：统计行数并输出
    local line_count=$(wc -l < "$file")
    echo "$file 的行数：$line_count"
    return 0  # 0表示执行成功
}

# 调用示例
fileLine note.txt  # 正确，输出：note.txt 的行数：xxx
fileLine          # 错误，输出用法提示
```

### 3.2.2 实战示例2：创建带名称的笔记文件

```bash
# 定义函数：newNote，参数1为笔记名称，功能：创建.md笔记并打开
newNote() {
    # 检查参数个数
    if [ $# -ne 1 ]; then
        echo "用法错误！正确用法：$0 笔记名称"
        echo "示例：$0 今日学习笔记"
        return 1
    fi

    # 定义笔记路径（桌面目录）
    local note_name="$1"
    local note_path="$HOME/Desktop/$note_name.md"

    # 检查文件是否已存在（避免覆盖）
    if [ -f "$note_path" ]; then
        echo "错误：笔记 $note_path 已存在"
        return 1
    fi

    # 创建文件并写入标题
    touch "$note_path"
    echo "# $note_name" > "$note_path"  # 写入Markdown标题
    echo "创建成功！笔记路径：$note_path"

    # 打开文件（系统兼容写法）
    if [ "$(uname)" = "Darwin" ]; then
        open "$note_path"  # macOS打开方式
    elif [ "$(uname)" = "Linux" ]; then
        xdg-open "$note_path"  # Linux打开方式
    fi
}

# 调用示例
newNote 今日学习笔记  # 桌面创建“今日学习笔记.md”并打开
```

## 3.3 bash函数的局部变量：避免污染全局环境

bash函数中，用 `local` 关键字定义的变量是“局部变量”，仅在函数内部有效；不加 `local` 的是“全局变量”，会影响函数外部的变量环境（新手重点避坑）。

```bash
testVar() {
    global_var="我是全局变量"  # 全局变量，函数外可访问
    local local_var="我是局部变量"  # 局部变量，仅函数内有效

    echo "函数内 - 全局变量：$global_var"
    echo "函数内 - 局部变量：$local_var"
}

# 调用函数
testVar

# 函数外访问变量
echo "函数外 - 全局变量：$global_var"  # 输出：我是全局变量
echo "函数外 - 局部变量：$local_var"  # 输出空（未定义）
```

新手强制要求：函数内的所有变量必须加 `local` 定义！否则会导致全局变量混乱（比如函数内的 `count` 会覆盖外部的 `count`）。

## 3.4 函数永久保存：关掉终端也能使用

直接在终端定义的bash函数，关掉终端后会消失。新手需将常用函数保存到bash的配置文件 `~/.bashrc`（Linux）或 `~/.bash_profile`（macOS）中，实现永久生效。

### 具体步骤（新手一步一步来）

1. 打开配置文件： Linux：终端输入`vim ~/.bashrc` macOS：终端输入 `vim ~/.bash_profile` （新手也可用文本编辑器打开：`open ~/.bash_profile`（macOS）、`xdg-open ~/.bashrc`（Linux））
2. 添加函数：在文件末尾粘贴写好的函数（如 `cdDesk`、`fileLine`）；
3. 保存文件： vim编辑器：按 `Esc` 键，输入 `:wq`（w=保存，q=退出），回车； 文本编辑器：直接点击“保存”；
4. 让配置生效：终端输入以下命令（无需重启终端） Linux：`source ~/.bashrc` macOS：`source ~/.bash_profile`

验证：关掉当前终端，重新打开，输入 `cdDesk`，若能跳转到桌面，说明函数已永久生效。

## 3.5 新手实用bash函数合集（直接抄）

```bash
# 1. 快速跳转到工作目录（替换为你的工作目录）
cdWork() {
    local work_dir="$HOME/Documents/工作"
    if [ -d "$work_dir" ]; then
        cd "$work_dir"
        ls -l
    else
        echo "错误：工作目录 $work_dir 不存在"
    fi
}

# 2. 批量重命名：当前目录txt文件→md文件
renameTxtToMd() {
    # 检查是否有txt文件
    local txt_files=(*.txt)
    if [ ! -f "${txt_files[0]}" ]; then
        echo "当前目录没有.txt文件"
        return 1
    fi

    # 批量重命名
    for file in *.txt; do
        local new_file="${file%.txt}.md"  # 删除.txt，添加.md
        mv "$file" "$new_file"
        echo "重命名：$file → $new_file"
    done
}

# 3. 查看指定端口占用（如 8080 端口）
checkPort() {
    if [ $# -ne 1 ]; then
        echo "用法错误！正确用法：$0 端口号"
        echo "示例：$0 8080"
        return 1
    fi

    local port="$1"
    # lsof查看端口占用，2>/dev/null 忽略错误输出
    lsof -i :"$port" 2>/dev/null || echo "$port 端口未被占用"
}
```

# 第四章：新手实操练习（巩固bash知识点）

以下练习覆盖核心语法和函数，新手建议逐题手动实操，加深理解：

## 练习1：变量与变量扩展

需求：定义变量 `file_path="$HOME/Desktop/test.txt"`，完成3个操作：
1. 打印变量值，格式：“文件路径：xxx”；
2. 用变量扩展获取文件名（test.txt），打印：“文件名：xxx”；
3. 用变量扩展获取目录路径（$HOME/Desktop），打印：“目录路径：xxx”。
    

## 练习2：条件判断

需求：判断 `$HOME/Desktop/test.txt` 是否存在，存在则打印行数，不存在则创建文件并写入“bash新手测试文件”。

## 练习3：循环

需求：遍历`$HOME/Desktop` 目录，区分“普通文件”和“目录”，分别打印：“文件：xxx”“目录：xxx”。

## 练习4：函数

需求：定义函数`delEmptyFile`，功能：删除当前目录下所有空文件（大小为0），要求：
1. 先检查是否有空文件；
2. 删除前打印要删除的文件名；
3. 删除完成后，打印“共删除x个空文件”。

# 第五章：新手常见问题与解决方案（bash专属）

## 问题1：定义变量时，等号加空格报错

错误写法：`name = "test"` → 报错：bash: name: command not found

解决方案：等号两边绝对不能有空格，正确写法：`name="test"`

## 问题2：函数调用提示“command not found”

原因：1. 函数名拼写错误；2. 函数未保存到 `~/.bashrc`（Linux）/ `~/.bash_profile`（macOS）；3. 未执行 `source` 让配置生效。

解决方案：1. 核对函数名拼写；2. 重新执行 `source ~/.bashrc`（Linux）或 `source ~/.bash_profile`（macOS）；3. 检查函数定义语法（如 `()` 和 `{}` 是否配对）。

## 问题3：循环遍历文件时，出现“*.txt: No such file or directory”

原因：目录下无.txt文件，bash将通配符 `*.txt` 当作普通字符串处理。

解决方案：在循环内添加文件判断：`if [ -f "$file" ]; then ... fi`，跳过非文件内容。

## 问题4：函数内变量覆盖外部变量

原因：函数内变量未加 `local` 定义，默认是全局变量。

解决方案：函数内所有变量必须加 `local` 定义，如 `local count=1`。