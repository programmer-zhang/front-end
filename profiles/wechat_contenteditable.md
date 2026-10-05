# 打造网页版微信(一): 属性 contenteditable 的用处

> 做聊天类应用，第一个绕不开的就是**消息输入框**。用 `<textarea>`多行、自适应高度、emoji、@提及、粘贴图片，样样都别扭。本篇就来聊聊我们为什么选用 `contenteditable`，以及围绕它踩过的坑。

> 本系列从输入框讲起，整体架构见 [WeHub 打造网页版微信](./wechat_overview.md)。

## 阅读本文您将收获
* `contenteditable` 是什么、和 `input` / `textarea` 的区别
* 如何基于它实现一个"能用的"聊天输入框
* 光标、粘贴、@提及、回车发送这几个高频坑的解决思路

## contenteditable 是什么
* 它是 `HTML5` 提供的一个**全局属性**，设置了 `contenteditable="true"` 的元素会被浏览器变成一个**可编辑区域**
* 取值有三种
	* `true`：可编辑
	* `false`：不可编辑（默认）
	* `plaintext-only`：可编辑，但粘贴/输入只会产生纯文本，不会带入富文本标签（**聊天场景强烈推荐**）
* 一个最简单的可编辑 `div`

```
<div id="editor" contenteditable="true">
    说点什么...
</div>
```

## 为什么不用 input / textarea
* 富文本能力：`textarea` 只能纯文本，无法承载 emoji 图片、@ 高亮、超链接等
* 自适应高度：`textarea` 高度需要自己算 `scrollHeight`，而 `contenteditable` 元素本身就会随内容自然增高
* 结构可控：`contenteditable` 内部是标准 `DOM`，可以插入任意节点（如 `<img>` 表情、`<a>` 链接）
* 光标/选区：`Selection` + `Range` 给了我们完全的控制权，可以实现选区加粗、@提及等高级交互

> 简单说：**简单场景用 textarea，聊天这种复杂交互用 contenteditable。**

## 输入框基本实现

```
<!-- Editor.vue -->
<template>
    <div
        ref="editor"
        class="editor"
        contenteditable="plaintext-only"
        :data-placeholder="placeholder"
        @keydown="onKeydown"
        @input="onInput"
        @paste="onPaste"
        @focus="onFocus"
        @blur="onBlur"
    ></div>
</template>
```

* `contenteditable="plaintext-only"` 从源头屏蔽富文本，避免用户从网页复制带一堆脏样式的内容进来
* 用 `:empty` 或 `data-placeholder` 伪元素实现 placeholder（原生元素没有 placeholder）

```
.editor:empty::before {
    content: attr(data-placeholder);
    color: #b2b2b2;
    pointer-events: none;
}
```

## 高频坑一：光标位置丢失
* 场景：输入框既有"手动输入"，又有"程序插入"（比如点击表情插入一段文本、@某人后自动补空格）
* 一旦我们用 `innerHTML` 重新赋值，光标就会跳到最前面，用户体验极差
* 解决思路：**保存光标 → 修改 DOM → 恢复光标**

```
// 保存光标：记录当前 Range 的起止位置
saveSelection() {
    const sel = window.getSelection()
    if (!sel.rangeCount) return null
    return sel.getRangeAt(0)
}

// 恢复光标
restoreSelection(range) {
    if (!range) return
    const sel = window.getSelection()
    sel.removeAllRanges()
    sel.addRange(range)
}

// 在光标处插入节点（如表情图片）
insertNodeAtCursor(node) {
    const range = this.saveSelection()
    if (!range) return
    range.deleteContents()      // 删除选中内容
    range.insertNode(node)
    // 插入后把光标移到节点后面
    range.setStartAfter(node)
    range.collapse(true)
    this.restoreSelection(range)
}
```

## 高频坑二：粘贴污染
* 用户从别处复制内容粘贴进来，会带一堆 `style`、`class`、外链图片，甚至脚本
* 解决：拦截 `paste` 事件，**只取纯文本**

```
onPaste(e) {
    e.preventDefault()
    // 优先取 clipboardData 中的纯文本
    const text = (e.clipboardData || window.clipboardData).getData('text/plain')
    if (!text) return
    // 手动插入到光标处（保留换行）
    document.execCommand('insertText', false, text)
}
```

* 注意：即使设置了 `plaintext-only`，某些浏览器对粘贴的处理仍不可靠，**双重保险更稳**

## 高频坑三：回车发送 vs 换行
* 聊天习惯：`Enter` 发送，`Shift + Enter` 换行（微信 PC 端也是这个逻辑）
* 在 `keydown` 里判断即可，注意中文输入法**选词回车**的干扰

```
onKeydown(e) {
    // 输入法组合中（选词阶段）不处理，否则会误发
    if (e.isComposing || e.keyCode === 229) return

    if (e.key === 'Enter') {
        if (e.shiftKey) {
            // 允许换行，交给默认行为
            return
        }
        e.preventDefault()
        this.sendMessage()
    }
}
```

> `e.isComposing` 是判断输入法组合状态的标准属性，中文输入法下**必须处理**，否则用户打拼音按回车选词会被当成发送。

## 高频坑四：@提及
* @提及的本质是：在输入框里插入一个**不可分割的整体**（一个人名节点）
* 用 `contenteditable="false"` 的 `span` 包裹，让它在编辑区内作为一个整体被删除/移动

```
<span class="mention" contenteditable="false" data-id="wxid_xxx">@张三</span>
```

* 插入后自动补一个空格，方便继续输入
* 发送前需要把 DOM 转成结构化数据：遍历子节点，普通文本拼接为字符串，mention 节点转成 `{ type: 'at', id, name }`
* 校验：如果 mention 节点被用户删掉了一半（比如只删掉 `@张三` 的一部分），需要在 `input` 事件里重建

## 内容读取与清空
* 读取：`this.$refs.editor.innerHTML` / `innerText`
* 清空：`this.$refs.editor.innerHTML = ''`
* 注意 `contenteditable` 元素**空的时候**可能残留一个 `<br>` 或 `&nbsp;`，判断是否为空要特殊处理

```
isEmpty() {
    const html = this.$refs.editor.innerHTML.trim()
    return html === '' || html === '<br>' || html === '<div><br></div>'
}
```

## 其他注意点
* `document.execCommand` 已被标记废弃，但**目前兼容性依然最好**，短期内仍是最优解，未来可迁移到 `InputEvent` + `Range`
* 移动端需要注意：`contenteditable` 元素聚焦后系统键盘弹出、`fixed` 布局会被顶起，需要监听 `resize` 做适配
* 不要把 `contenteditable` 元素放进 `v-model` 里直接双向绑定，`v-model` 只适用于表单元素，会失效

## 写在最后
* 一个"能用"的输入框，核心就三件事：**可编辑、控光标、清粘贴**
* 把这几块处理好，聊天输入框的体验就稳了
* 下一篇我们来讲讲怎么用 `WeTool` 真正把微信接进来：[打造网页版微信(二): 利用 wetool 接入微信](./wechat_wetool.md)
