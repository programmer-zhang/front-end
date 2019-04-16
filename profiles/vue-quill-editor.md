> 今天推荐一个好用的VUE富文本编辑器插件---`vue-quill-editor`

> [官方github文档](https://github.com/surmon-china/vue-quill-editor)

## 安装
* CDN安装

```
<link rel="stylesheet" href="path/to/quill.core.css"/>
<link rel="stylesheet" href="path/to/quill.snow.css"/>
<link rel="stylesheet" href="path/to/quill.bubble.css"/>
<script type="text/javascript" src="path/to/quill.js"></script>
<script type="text/javascript" src="path/to/vue.min.js"></script>
<script type="text/javascript" src="path/to/dist/vue-quill-editor.js"></script>
<script type="text/javascript">
  Vue.use(window.VueQuillEditor)
</script>
```

* npm 安装

```
npm install vue-quill-editor
```

## 引入
* 全局引入

```
//main.js
import Vue from 'vue'
import VueQuillEditor from 'vue-quill-editor'

// require styles
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'

Vue.use(VueQuillEditor)
```
* 组件内引入

```
<template>
	<div class="editor-content">
		<quill-editor v-model="content"
                ref="myQuillEditor"
                :options="editorOption"
                @blur="onEditorBlur($event)"
                @focus="onEditorFocus($event)"
                @ready="onEditorReady($event)">
		</quill-editor>
	</div>
</template>
<script>
import { quillEditor } from 'vue-quill-editor'

export default {
	data() {
		return {
			content: '<h2>I am Example</h2>',
	        editorOption: {
	          // some quill options
	        }
		}
	},
	components: {
		quillEditor
	}
}
</script>
<style lang="scss">
	@import 'quill/dist/quill.core.css';
	@import 'quill/dist/quill.snow.css';
	@import 'quill/dist/quill.bubble.css';
</style>
```
