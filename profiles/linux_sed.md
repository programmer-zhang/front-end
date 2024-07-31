# Linux 开发 -- sed 使用指南
> `sed` 是一个非常灵活的工具，适用于各种文本处理任务。

> 不同于 `vim` 是交互式文本编辑模式，通过键盘交互进行文本编辑。`sed` 是流式编辑模式，直接通过指令的形式进行文本编辑。

> 本文使用 `ChatGPT` 辅助编写

## 基本语法

`sed` 的基本语法如下：

```bash
sed [选项] '命令' 文件名
```

- **选项**：用于控制 `sed` 的行为，如 `-n` 禁用自动输出等。
- **命令**：`sed` 执行的操作。
- **文件名**：要处理的文件名。如果没有指定文件名，`sed` 会从标准输入读取数据。

## 常用命令

### 1. 替换（`s`）

替换是 `sed` 最常用的操作。它的基本语法是：

```bash
sed 's/旧字符串/新字符串/' 文件名
```

- `s` 表示替换操作。
- `旧字符串` 是要被替换的内容。
- `新字符串` 是替换后的内容。

#### 示例

假设有一个名为 `example.txt` 的文件，内容如下：

```
Hello World
Hello sed
```

要将文件中的 `Hello` 替换为 `Hi`，可以使用以下命令：

```bash
sed 's/Hello/Hi/' example.txt
```

输出将是：

```
Hi World
Hi sed
```

### 2. 替换所有匹配（`s/旧字符串/新字符串/g`）

默认情况下，`sed` 只替换每行的第一个匹配。如果要替换所有匹配，可以使用 `g` 标志。

#### 示例

```bash
sed 's/Hello/Hi/g' example.txt
```

输出将是：

```
Hi World
Hi sed
```

### 3. 删除行（`d`）

要删除特定的行，可以使用 `d` 命令。

#### 示例

删除 `example.txt` 中的第二行：

```bash
sed '2d' example.txt
```

输出将是：

```
Hello World
```

### 4. 插入行（`i`）

使用 `i` 命令在指定行之前插入新行。

#### 示例

在文件的第一行之前插入一行：

```bash
sed '1i\
This is a new line
' example.txt
```

输出将是：

```
This is a new line
Hello World
Hello sed
```

### 5. 追加行（`a`）

使用 `a` 命令在指定行之后追加新行。

#### 示例

在文件的第二行之后追加一行：

```bash
sed '2a\
This is another new line
' example.txt
```

输出将是：

```
Hello World
Hello sed
This is another new line
```

### 6. 只输出匹配的行（`-n` 和 `p`）

使用 `-n` 选项禁用自动输出，然后使用 `p` 命令打印匹配的行。

#### 示例

打印 `example.txt` 中包含 `Hello` 的行：

```bash
sed -n '/Hello/p' example.txt
```

输出将是：

```
Hello World
Hello sed
```