---
layout: post
title: shell编码规范
date: 2018-08-22
tags: shell
---
## 章节

> 0. 一般信息
> 1. 背景(Background)
> 2. Shell文件和解释器调用(Shell Files and Interpreter Invocation)
> 3. 书写布局(Layout）
> 4. 命名规范(Naming Conventions)
> 5. 注释(Comments)
> 6. 格式(Formatting)
> 7. 特性和缺陷(Features and Bugs)
> 8. 调用命令(Calling Commands)
> 9. 环境(Environment)
> 10. 其他最佳实践(Best Practice)

### 0. 一般信息
 
    本文档适用于Bash 3.0及以上版本，不包括4.0新增特性
    
    文档是Bash编程规范，不是POSIX Shell编程规范
    
    章节分类和内容组织依据Google Shell Style Guide
    
    仅包括bash语言(包括内部命令)的内容，不包括外部命令的使用建议，如awk。
    
    不包括特定应用的代码实现，如：md5生成方式。

### 1. 背景(Background)

`1.1 用哪个Shell`

    [RULE 1-1] Bash 是公司唯一指定的Shell语言，版本在4.0（含）以上

**查看bash版本的方法**

```
$ bash --version
bash --version
GNU bash, version 4.1.2(1)-release (x86_64-redhat-linux-gnu)
Copyright (C) 2009 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>

[RULE 1-2] 请遵守此规范，或保证和原有代码风格、语法、规范一致
解释：对历史代码，不做强制打平到当前代码规范的要求
```

`1.2 何时不选择使用 Shell`

    [ADVISE 1-1] Shell仅用于开发小工具(small utilities)和包装脚本(wrapper scripts)
  
    [ADVISE 1-2] 如果仅仅调用其他程序，或是极少的数据处理，Shell是合适的选择
  
    [ADVISE 1-3] 如需要使用hash（Bash3.x以下原生不支持）、嵌套array，建议用其他语言实现
  
    [ADVISE 1-4] 对性能要求较高的场景，建议用其他语言实现

### 2. Shell文件和解释器调用(Shell Files and Interpreter Invocation)

`2.1 扩展文件名`

    [RULE 2-1] 可执行Shell脚本无需后缀或使用后缀.sh
  
    [RULE 2-2] 库文件必须用后缀.sh

`2.2 SUID/SGID`

    [RULE 2-3] SUID、SGID禁止使用，需要的时候使用 sudo
  
    解释：防止脚本静默访问没有权限的资源，如果必须访问请通过sudo显示指定

`2.3 Usage`

    [RULE 2-4] 可执行脚本在加-h参数调用时应打印Usage
  
    示例：可以在系统里执行awk -h观察此程序的Usage，
  
    提示：脚本输入参数的处理可以参考：getopt、case等

```
$ awk -h
Usage: awk [POSIX or GNU style options] -f progfile [--] file ...
Usage: awk [POSIX or GNU style options] [--] 'program' file ...
POSIX options:        GNU long options:
    -f progfile                 --file=progfile
    -F fs                       --field-separator=fs
    -v var=val                  --assign=var=val
    -m[fr] val
    -O                          --optimize
    -W compat                   --compat
    -W copyleft                 --copyleft
    -W copyright                --copyright
    -W dump-variables[=file]    --dump-variables[=file]
    -W exec=file                --exec=file
    -W gen-po                   --gen-po
    -W help                     --help
    -W lint[=fatal]             --lint[=fatal]
    -W lint-old                 --lint-old
    -W non-decimal-data         --non-decimal-data
    -W profile[=file]           --profile[=file]
    -W posix                    --posix
    -W re-interval              --re-interval
    -W source=program-text      --source=program-text
    -W traditional              --traditional
    -W usage                    --usage
    -W use-lc-numeric           --use-lc-numeric
    -W version                  --version
```

### 3. 书写布局(Layout)

`3.1 布局(Layout)`

    [ADVISE 3-1] 按照开关命令，环境变量，source文件，常量，变量，函数，主函数/主逻辑的顺序书写脚本 

```
#!/bin/bash 
# 1. 开关 
set -x # set -o xtrace 
set -e # set -o errexit 
set -u # set -o nounset 
set -o pipefail 
```
`3.2 环境变量`

    PATH=/home/abc/bin:$PATH export PATH 

`3.2 source文件`

    source some_lib.sh

`3.3. 常量`

    readonly PI=3.14 

`3.5 变量`

    my_var=1 

`3.6 函数`

```
# Usage
function usage() {
} 

# 函数注释，格式参考注释章节 
function my_func1() {
} 

# 函数注释，格式参考注释章节 
function my_func2() {
} 

# 函数注释，格式参考注释章节 
function main() {
} 
```

`3.7 主函数/主逻辑`

   main "$@"

### 4. 命名规范(Naming Conventions)

4.1 常量/环境变量名(Constants and Environment Variable Names)
[RULE 4-1] 全部大写，下划线分割，且在文件头部声明
[RULE 4-2] 所有需要export的变量都要大写
#配置文件常量
FILE_PATH="/home/spider/conf/"
#全局常量
declare -r MAX_PATH_SIZE=256
#环境变量
export PYPATH="/home/spider/python"
4.2 source文件名(Source Filenames)
[RULE 4-3] 被source的文件，其文件名全部小写，下划线分割单词
source lib/color_print.sh
4.3 常量(Read-only Variables)
[RULE 4-4] 使用readonly或declare -r，明确声明常量，建议只使用readonly的方式
# 使用readonly声明方式
readonly NAME='spider'
# 使用declare声明方式
declare -r NAME='spider'
4.4 变量名(Variable Names)
[RULE 4-5] 命名全部小写，下划线分割单词
[ADVISE 4-1] 多个关联的变量名在含义上保持风格一致，比如for in循环的时候
# $url , $url_list 含义一致
for url in ${url_list}; do
    something_with "${url}"
done
4.5 函数名(Function Names)
[RULE 4-6] 命名全部小写，下划线分割单词
[RULE 4-7] 小括号必须在函数名以后，不能有空格
[RULE 4-8] 函数的左大括号与函数名在同一行
[RULE 4-9] 使用function关键字
[ADVISE 4-2] 使用 :: 区分包、库
function my_func() {
    ...
}
function lib::my_func() {
    ...
}
4.6 local变量(Use Local Variables)
[RULE 4-10] 使用local声明函数内部使用的变量
# Good
function my_func2() {
    # 声明和赋值在一行
    local name="$1"
    # 声明和赋值拆为2行
    local my_var
    my_var="$1"    
}

# Bad
function my_fun3() {
    # 不要这样用，$?为local的返回值，而不是my_func
    local my_var="$(my_func)"
    [[ $? -eq 0 ]] || return
}
4.7 main函数
[ADVISE 4-3] 对于长脚本，使用main函数放到所有函数声明的后面
# 参见第三章例子
function main() {
}
main "$@"

### 5. 注释(Comments)
5.1 注释样式
[RULE 5-1] 采用单行注释#
# comments
[ADVISE 5-1] 使用utf8编码（尽量用英文注释）
[ADVISE 5-2] 仅调试时才使用多行注释，多行注释建议使用 :<<\###的方式，方便开关
:<<\###  下面是本次要注释的内容
    do_something
    do_other_thing
###

如果需要执行这段代码，则
#:<<'###'  下面是本次要注释的内容
    do_something
    do_other_thing
###
5.2 文件头注释(File Header)
[RULE 5-2] 脚本第一行为 #!/bin/bash
# Good
#!/bin/bash

# Bad
#!/bin/sh
#!/bin/bash -x
[RULE 5-3] 脚本顶部必须有对脚本功能的说明
[ADVISE 5-3] 注释内容可包括：版权说明、作者、时间、代码功能、用法说明、全局变量、命令行参数、返回值
#!/bin/bash
#
# Copyright (c) 2016 baishancloud.com, Inc. All Rights Reserved
#
# Author: Adam
# Date: 2016/12/20
# Brief:
#   Backup mysql databases with innobackupex.
# Globals:
#   BACKUP_DIR
# Arguments:
#   None
# Returns:
#   succ:0
#   fail:1
5.3 函数注释(Function Comments)
[RULE 5-4] 函数需要有注释。
[ADVISE 5-4] 注释内容可包括：函数功能，全局变量，参数，返回值。对于简单的函数，如：main,usage可以不加注释或者只加1行注释
#######################################
# Brief:
#   Cleanup files from the backup dir.
# Globals:
#   BACKUP_DIR
# Arguments:
#   None
# Returns:
#   None
#######################################
function cleanup() {
    rm -rf ${BACKUP_DIR}
}

# usage
function usage() {
}

function main() {
}
5.4 TODO 注释(TODO Comments)
[ADVISE 5-5] 使用 “#TODO(添加者)” 标注将来还要做的工作
# TODO(Someone): xxx (issue ####)

### 6. 格式(Formatting)
6.1 缩进
[RULE 6-1] 用4个空格，不要用tab
# 解释：不同编辑器对TAB的设定可能不同，使用TAB容易造成在一些编辑器下代码混乱，所以建议一率转换成空格。
# 在vim下，建议打开如下设置：
#    :set tabstop=4 设定tab宽度为4个字符
#    :set shiftwidth=4 设定自动缩进为4个字符
#    :set expandtab 用space自动替代tab
# 对于原有脚本，可以使用:set list查看是否存在tab，并使用sed或vim的替换命令进行统一转换。
6.2 分号
[RULE 6-2]行尾不使用;，除非必要
# 解释：通常情况下，行尾的分号没有意义。

# 特例：find工具
find . -type f -exec grep "something" {} \;
6.3 单行长度
[ADVISE 6-1] 一行不要超过80个字符，可以使用引号、 \ 、here document 等方式换行
# 解释：由于屏幕宽度有限，单行长度不宜超过80字符。
# 80个字符的长度如下：
#0123456789012345678901234567890123456789012345678901234567890123456789012345678

# Good
# 使用here doc换行。
cat <<END
I am an exceptionally long
string.
END

# 使用引号换行。
long_string="I am an exceptionally
    long string."

# 使用\换行。
echo "An exceptionally long string." \
    | grep "long string"
[RULE 6-3] 文件名和路径名不能折行
# 解释：折行会破坏文件名和路径名完整性，极易引发错误。

# Good
# 使用拼接来缩短单行长度。
dir_path="/home/work/workspace/myspace"
file_name="this_is_a_file"
file_path="${dir_path}/${file_name}"

# Bad
# 对路径名使用引号折行。这会导致路径中包含换行，路径错误。
file_path="/home/work/workspace/myspace
/this_is_a_file"

# 对文件名使用\折行。除非破坏缩进顶格换行，否则都会在路径中引入缩进的空格，路径错误。
file_name="this_is_\
    a_file"
6.4 管道(Pipe)
[RULE 6-4] 如果使用一个或多个管道导致超长，只允许使用 \ 换行
# 解释：超长的管道不易拆分，而引号和here doc等折行方式通常仅适用于字符串，因此使用\换行。

# Short commands
command1 | command2
# Long commands
command1 \
    | command2 \
    | command3 \
    | command4
6.5 条件和循环(Loops and Condition)
[RULE 6-5] ; do 和 ; then，必须和 for、if、elif 、while 在一行，else、fi、done独自一行，并与for、if、while垂直首部对齐
# 解释：do和then与C语言中的{类似，fi和done与}类似，也采用类似的折行与对齐方式，便于阅读。

for dir in ${dirs_to_cleanup}; do
    if [[ -d "${dir}/${ORACLE_SID}" ]]; then
        echo "Cleaning up old files in ${dir}/${ORACLE_SID}"
        rm "${dir}/${ORACLE_SID}/"*
        if [[ "$?" -ne 0 ]]; then
            echo "Clean up old files error" >&2
        fi
    else
        mkdir -p "${dir}/${ORACLE_SID}"
        if [[ "$?" -ne 0 ]]; then
            echo "Create directory ${dir}/${ORACLE_SID} error" >&2
        fi
    fi
done
6.6 Case语句
[RULE 6-6]不能混用单行和多行写法
[RULE 6-7]单行写法需要在)后 和 ;;前增加1个空格
[RULE 6-8]多行写法，需要将每个语句拆成1行
# 解释：case语句往往较长，若不保持格式统一，会对阅读造成很大障碍。

# Good
# 单行写法，适用于所有分支都较为简单的场景。
verbose='false'
aflag=''
bflag=''
files=''
while getopts 'abf:v' flag; do
    case "${flag}" in
        a) aflag='true' ;;
        b) bflag='true' ;;
        f) files="${OPTARG}" ;;
        v) verbose='true' ;;
        *) error "Unexpected option ${flag}" ;;
    esac
done

# 多行写法，适用于部分或全部分支较为复杂的场景。
case "${expression}" in
    a)
        variable="xxx"
        some_command "${variable}" "${other_expr}"
        ;;
    absolute)
        actions="relative"
        another_command "${actions}" "${other_expr}"
        ;;
    *)
        error "Unexpected expression '${expression}'"
        ;;
esac

# Bad 
# 升级或新增某一分支时，因较为复杂，使用了多行写法，但其它简单分支使用了单行写法，造成混用。
verbose='false'
aflag=''
bflag=''
files=''
while getopts 'abf:hv' flag; do
    case "${flag}" in
        a) aflag='true' ;;
        b) bflag='true' ;;
        f) files="${OPTARG}" ;;
        h)
           usage
           exit 0
           ;;
        v) verbose='true' ;;
        *) error "Unexpected option ${flag}" ;;
    esac
done
6.7 引用(quoting)
[RULE 6-9] 引用包含变量的字符串、命令替换、空格、shell元字符
# 解释：空格和tab等会造成字符串或参数被拆分成多个，引发错误，$、&等元字符更会引发各种控制逻辑。
#       变量以及命令替换的结果中，往往也会包括空格和各种shell元字符。
#       因此，这些都需要加上引号。

# Good
# 涉及空格，需要加引号。
# 如果不加引号，s1中的world会被当成独立命令并报错，s2中更是会删除名为file的文件，s1和s2都不会被赋值。
s1="Hello world"
s2="I rm file"

# 涉及包含变量的字符串，需要加引号。
s3="${s1}. ${s2}"

# 涉及命令替换，需要加引号。
dir_path="$(dirname $BASH_SOURCE)"

# Bad 
# 涉及shell元字符，未加引号。&会被视为控制字符，其后的url参数全部无效，并且将wget进程挂到后台。
wget http://ip138.com/ips1388.asp?ip=8.8.8.8&action=2
[ADVISE 6-2] 不引用命令选项、路径名
# 解释：通常在命令行中执行命令时，不会给命令选项以及路径名加引号，脚本中也建议保持一致。
#       但如果路径中存在空格等不加引号就可能出错的情况，仍然需要加上引号。

# Good 
# 命令选项和路径名不加引号。
ls -l /home/work

# Bad 
# 路径中存在空格，不加引号会被当成两个路径并引发预期外的错误。
ls -l /home/work/my space/workspace

# 使用变量拼接了命令选项，并按RULE 6-9加了引号，这会造成命令选项无法被识别。确认作为命令选项时，不要引用。
param="-l -r -t"
ls "${param}" /home/work    #error
[RULE 6-10] 双引号用于引用需要替换的部分；单引号用于引用不替换的部分
# 解释：双引号中的变量、命令等都会被替换，大部分元字符可生效；单引号中不进行替换，大部分shell元字符不生效。

# Good
# s1和s2的值都是当前目录绝对路径。
s1="$PWD"
s2="$(pwd)"

# 建议引用单词字符，不是强制的。
readonly USE_INTEGER='true'

# 单引号中shell元字符无需转义，双引号中需要转义，否则会被解释为其它含义。
echo 'Hello stranger, and well met. Earn lots of $$$'
echo "Process $$: Done making \$\$\$."

# Bad 
# s3和s4的值分别是$PWD和$(pwd)这两个字符串，除非有特殊需求，否则是错误的。
s3='$PWD'
s4='$(pwd)'
[ADVISE 6-3] 不引用整数字面量
# 解释：根据习惯，整数字面量不使用引用，以示与纯数字字符串区分。

# Good  
# 整数字面量不引用，纯数字字符串需要引用。
value=32
id="0123456789012345"

# Bad 
# 使用了命令替换，即使是整形，但仍然应该引用。
number=$(generate_number)
[RULE 6-11] 使用"$@"、"$*"，$@和$*必须被双引号引用
# 解释：由于参数往往有多个，而每个参数中又很可能有空格或tab，若不引用则往往获取的结果和预期不一样。

# Good 
inputs="$*"
main "$@"

# Bad
# 由于$@没加引号，实际输出是每行1个单词，共计4行，而非预期的每行一个参数，共计2行。
# 函数注释，格式参考注释章节。
function dump() {
   for i in $@; do
        echo "${i}"
    done
}
dump "hello world" "bye bye"
[ADVISE 6-4] 尽量使用 "$@"， 除非你充分的有理由使用 "$*"
# 解释："$@"保持原样传递参数，而"$*"则会把所有参数合并成一个字符串，多数情况下我们需要的是"$@"的逻辑。

# 向main函数传递参数时，通常都应当使用"$@"。
main "$@"
6.8 变量扩展(Variable expansion)
[RULE 6-12] 对于自定义变量，使用"${var}",不用 "$var"
# 解释：自定义变量往往需要参与各种字符串拼接，如果不加括号，既不利于阅读，也很容易导致逻辑错误。

# Good
prefix="pre"
echo "${prefix}_dir/file"

# Bad
# 会把prefix_dir视为变量名，由于无定义，会输出/file，若设置了set -u，则会报错。
prefix="pre"
echo "$prefix_dir/file"

### 7. 特性和缺陷(Features and Bugs)

7.1 环境变量赋值(Environment Variable Assignment)
[RULE 7-1] 环境变量赋值中，严格禁止使用相对路径。
# 解释：环境变量会应用到被调用的其他命令上。若其他命令改变了当前路径，环境变量中的相对路径会发生变化。

# Good
PATH=$(pwd)/bin:$PATH
LD_LIBRARY_PATH=$(pwd)/../lib:$LD_LIBRARY_PATH
export PATH
export LD_LIBRARY_PATH

# Bad
PATH=./bin:$PATH
LD_LIBRARY_PATH=../lib:$LD_LIBRARY_PATH
export PATH
export LD_LIBRARY_PATH
7.2 命令替换(Command Substitution)
[RULE 7-2] 使用 $() 代替 反引号 ``
# 解释：$() 语法可读性更好，且支持嵌套。
7.3 管道对接while(| while read)
[RULE 7-3] 不使用管道对接 while read，使用 进程替换
# 解释：每个管道符‘|’都会开启一个子shell，子shell中的变量，以及对变量的操作在父shell中不可见

# Good
total=0
last_file=''
while read count filename ignored; do
   $(( total += $count ))
   last_file="${filename}"
done < <(uniq -c file.txt) # 使用进程替换，这样不会生成隐形的子shell
echo "Total = ${total}"
echo "Last one = ${last_file}"

# Bad
last_line='NULL'
your_command | while read line; do
   last_line="${line}"
done
echo "${last_line}" # 依然输出 'NULL'，而不是 `your_command` 赋予的值
7.4 test , [ 和 [[
[RULE 7-4] 使用 [[ ]] 代替 [、test。但在测试代码中可以使用test
[RULE 7-5] 明确的使用 -n、-z，表明意图；不能使用[[ xx"${my_var}" = xx"some_string" ]]这种形式
# Good 
# 使用 -z、-n 判断变量是否为空（或存在）
if [[ -z "${my_var}" ]]; then
    do_something
fi

# 判断变量是否相等，使用这种形式
[[ "${my_var}" = "some_string" ]]
# 不使用这种形式
[[ xx"${my_var}" = xx"some_string" ]]

# Bad
if [[ "${var}" ]]; then
    do_something
fi
if [[ "${my_var}" = "" ]]; then
    do_something
fi

# 在测试代码中，可以使用test命令，更加直观
it_displays_usage_with_hyphen_and_h() {
   usage=$(bash runit -h | head -n1)
   test "${usage}" = "${usage_result}"
}
7.5 通配符(Wildcard Expansion of Filenames)
[RULE 7-6] 使用文件通配符时必须指明路径，避免由于含有 -r、-f 等这类文件名，造成风险
# Good
# 明确路径，避免误删
[work@dev ~]$ touch -- '-r' '-f' 'foo2' 'bar2'
[work@dev ~]$ mkdir rich
[work@dev ~]$ rm ./*
rm: cannot remove './rich': Is a directory    # 文件夹没有被删
[work@dev ~]$ ls -l
total 4
drwxrwxr-x 2 work work 4096 Mar 27 20:16 rich

# Bad 
# 危险行为，'*' 中可能包含了 '-rf'
[work@dev ~]$ touch foo bar
[work@dev ~]$ touch -- '-r' '-f'
[work@dev ~]$ mkdir loser
[work@dev ~]$ ls -l
total 0
-rw-rw-r-- 1 work work 0 Mar 27 20:08 bar
-rw-rw-r-- 1 work work 0 Mar 27 20:08 -f
-rw-rw-r-- 1 work work 0 Mar 27 20:08 foo
-rw-rw-r-- 1 work work 0 Mar 27 20:08 -r
drwxrwxr-x 2 work work 4096 Mar 27 20:12 loser/
# 此处 '*' 中包含了 '-r' '-f'，所以 'loser' 文件夹被误删了
[work@dev ~]$ rm *
[work@dev ~]$ ls -l
total 0
-rw-rw-r-- 1 work work 0 Mar 27 20:08 -f
-rw-rw-r-- 1 work work 0 Mar 27 20:08 -r
[work@dev ~]$ rm -- -r -f
[work@dev ~]$ ls -l
total 0
[RULE 7-7] 如果作为参数传递，需要引用或转义。当通配符本身与其他命令的参数重复时，注意通配符优先的问题。
# Good
[work@dev ~]$ echo 2 \* 3  |  bc
6
[work@dev ~]$ echo '2 * 3'  |  bc
6

# Bad
[work@dev ~]$  touch  + # touch一个文件名为“+”的文件
[work@dev ~]$  echo  2 * 3
2 + 3
[work@dev ~]$ echo 2 * 3  |  bc     # “*”先被解析为了“+”
5
7.6 算数计算(Arithmetic)
[RULE 7-8] 使用$(()), 不使用let
# Good
# i自增1
$(( i += 1 ))
# 复杂的算数计算
min=5
sec=30
echo $(( (min * 60) + sec ))

# Bad
let "i += 1"

min=5
sec=30
ret=0
let "ret = ( min * 60 ) + sec"
echo "${ret}"
7.7 函数返回值(Function Return)
[RULE 7-9] 函数返回值0代表成功，非零代表其他含义
[RULE 7-10] return仅做返回值用，禁止用函数返回值返回计算结果
解释：return只能返回0-255的数字，如果有其他需要返回的内容，使用对命令输出捕获的方式

### 8. 调用命令(Calling Commands)

8.1 检查返回值(Checking Return Values)
[ADVISE 8-1] 检查返回值。建议开启set -e选项。
# cd命令的返回值，被'&&'判断，只有cd成功了，才rm
cd /a/dir && rm file

# 或者使用 `if/else`
if ! mv "${file_list}" "${dest_dir}/"; then
   echo "Unable to move ${file_list} to ${dest_dir}" >&2
   exit 1
fi
[ADVISE 8-2] 使用 ${PIPESTATUS[@]} 数组检查管道中命令的返回值。建议开启set -o pipefail选项。
tar -cf - ./* | ( cd "${dir}" && tar -xf - )
if [[ "${PIPESTATUS[0]}" -ne 0 || "${PIPESTATUS[1]}" -ne 0 ]]; then
   echo "Unable to tar files to ${dir}" >&2
fi
# 注意， ${PIPESTATUS[@]} 数组在下一个命令执行完毕后，将清空
tar -cf - ./* | ( cd "${DIR}" && tar -xf - )
return_codes=(${PIPESTATUS[*]})
if [[ "${return_codes[0]}" -ne 0 ]]; then
    do_something
fi
if [[ "${return_codes[1]}" -ne 0 ]]; then
    do_something_else
fi
8.2 内建与外部命令(Builtin Commands vs. External Commands)
[RULE 8-1] 优先使用内建命令而不是外部命令
# 解释: 内建命令更健壮. 外部命令存在于磁盘上, 而磁盘是一个故障率很高的设备, 如果挂掉, 则命令不可用.

# 算术表达式
# Good:
addition=$((${X} + ${Y}))

# Bad:
addition="$(expr ${X} + ${Y})"

# 字符串操作
# Good:
substitution="${string/#foo/bar}"

# Bad:
substitution="$(echo "${string}" | sed -e 's/^foo/bar/')"

# 读取文件
# Good:
var=$(</proc/loadavg)

# Bad:
var=$(cat /proc/loadavg)

### 9. 环境(Environment)

9.1 STDOUT 与 STDERR
[RULE 9-1] 错误信息输出至STDERR，以更容易区分正常消息
[ADVISE 9-1] 推荐用函数来输出错误
# print error message to stdout
function err() {
    echo "[$(date +'%Y-%m-%dT%H:%M:%S%z')]: $@" >&2
}
if ! do_something; then
    err "Unable to do_something"
    exit "${E_DID_NOTHING}"
fi

### 10. 其他最佳实践（Best Practice）

10.1 set
[ADVISE 10-1] 建议开启 set -e, set -u, set -o pipefail 开关，帮助定位问题。
#!/bin/bash

set -u  # 使用的变量必须提前定义过
set -e    # 所有非0的返回状态都需要捕获
set -o pipefail  # 管道间错误需要捕获

var="NotNull" # 如果此行注释掉，那么脚本将不会继续执行
echo "${var}"

# 错误需要捕获，否则不会继续执行
false || {
   echo "Something false."
}

# 管道间遇到的第一个错误，被捕获
true | false | true || {
   echo "Something false in the pipe."
}
echo "End"
10.2 临时文件
[ADVISE 10-2] 不使用临时文件，避免异常退出时垃圾文件的残留（日积月累后，无人知道哪些文件可删除），建议使用进程替换技术替换文件，将内容放入内存中。
# 直接diff两个命令的结果
[work@dev ~]$ diff <(echo x) <(echo y)
1c1
< x
---
> y
#!/bin/bash

# 直接使用pstree，在每一行前加了行号
while read i; do
   echo -e "$((++j))" "\t $i"
done < <( pstree )

# 使用 here-document，直接对多行文本进行逐行处理
while read line; do
   echo "this is $line"
done < <(cat <<EOF
xxx
yyy
zzz
EOF)

# 使用 here-string
while read line; do
   echo "this is $line"
done <<< "xxx
yyy
zzz"
10.3 幂等性
[ADVISE 10-3] 尽量确保脚本执行的幂等性，保证逻辑不会重复被执行两次以上。
#!/bin/bash
# 注1：此脚本框架例子，利用了临时文件保证幂等，依赖临时文件的持久化
# 注2：当脚本异常终止时（kill -9或机器死机），脚本中的rm命令可能不会被执行，从而影响下一次运行

set -eu
set -o pipefail

lockfile=/home/work/dev/mylock/lock.pid
if (set -C; echo $$ > ${lockfile}) 2>/dev/null; then
   # set -C 使已存在的文件不能再被写
   # echo 生成锁文件，将pid放入其中
   # 当此lock文件存在时，if返回失败，跳到else
   # trap保证了脚本异常中断时，释放锁文件（删）
   trap 'rm ${lockfile}; exit $?' INT TERM EXIT    
   {
       echo "my critical code..."  # 此处是正式的脚本代码
       echo "my critical code..."
       echo "my critical code..."
   }
    rm ${lockfile}          # 正式代码运行完了，释放锁文件
   trap - INT TERM EXIT  # 恢复trap的设置（如在脚本最后时，非必要恢复）
   exit 0
else
   # 锁文件生效，会跳到此处, 打印错误信息
   echo "${lockfile} exist, pid $(<${lockfile}) is running."  
   exit 1
fi
