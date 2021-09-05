> 此文件主要留存日常代码书写过程中的 框架架构依赖 相关的报错与解决方案，部分方案参考自网络，如有侵权，请联系chinajnzhang@hotmail.com删除

## 包管理 ERROR
### grunt angular项目sass报错
* sass模块报错
	* 重装sass
		* gem install sass
		* sass -v 查看是否安装成功
		* 权限问题：sudo gem install sass
	* 重装ruby
		* 使用homebrew获得ruby：brew install ruby

### npm包缺失报错
* 删除项目中的node_modules 并尝试重新 npm install,如重新安装之后还是报相同错误，请尝试下面的做法
* 删除本地项目node_modules包，复制相同项目相同操作系统的依赖包
* 指定依赖和版本安装
	* 如：缺少webpack-merge包,首先查看是否安装了webpack-merge模块`npm ls --depth=0` 

	* 如果没有重新安装指定依赖和版本
`npm install webpack-merge --save-dev`

### `webpack-dev-server --inline --progress --config build/webpack.dev.conf.js` 终端报错
* 原因：新版的webpack-dev-server 3.1.14 存在问题。
* 解决方法：package.json 中指定 webpack-dev-server 低版本号
	* 重装 webpack-dev-server模块

```
1）npm uninstall webpack-dev-server
2）npm install webpack-dev-server@2.9.1
3）npm run dev
```

## Swiper EROOR
### swiper在Vue项目中引入失败
* swiper若是本地js引入，需要放在`Vue  static`文件夹中，否则会被Vue打包后识别不到。

### swiper滑动失效
* 使用swiper会碰到模块滑动失败的问题，这种问题在vue、angular这种框架下发生几率高，原因在于模块数据未加载完成就开始初始化swiper导致
* 解决方案：
	* 一：使用settimeout()进行延时，不易过长，300毫秒足够，但是要注意的是js的作用域问题
	* 二：使用swiper自带的属性进行自动初始化
	
```
swiper1 = new Swiper('.swiper-container', {  
    initialSlide :0,  
    observer:true,//修改swiper自己或子元素时，自动初始化swiper  
    observeParents:true//修改swiper的父元素时，自动初始化swiper  
});  
```
* 个人建议使用第一种，两种结合来用也可以，第一种方案可以完美解决失效问题。

## Git ERROR
### git 初次提交远程仓库出现refusing to merge unrelated histories报错
* 先pull，因为两个仓库不同，报错 ` refusing to merge unrelated histories`，无法pull

* 因为他们是两个不同的项目，要把两个不同的项目合并，git需要添加一句代码，在git pull，这句代码是在git 2.9.2版本发生的，最新的版本需要添加 `--allow-unrelated-histories`

* 假如我们的源是origin，分支是master，那么我们需要这样写`git pull origin master --allow-unrelated-histories`需要知道，我们的源可以是本地的路径

### 解决每次push都要输入password和username的方法
* push 到 Github 每次都要输入密码，原因是使用了 Https 的方式添加远程仓库
* 解决方案：
	* 创建SSH Key，把id_rsa.pub中的内容添加到 Github 的SSH keys。
	* 或者使用本机已存在的SSH key
	* `git remote rm origin`
	* `git remote add origin git@github.com:XXX.git`
	* 或者直接修改项目文件夹 `.git/config` 修改 `remote url`
### Git git pull 本地文件夹权限问题
* 本地文件夹权限未分配
	* `sudo chmod -R 755 profiles`
* git 权限未分配
	* `sudo chmod -R 777 .git`

### Git报错
* 报错内容: 

```
There is no tracking information for the current branch. Please specify which branch you want to merge with.
```

* 原因: 本地仓库未与远程仓库相关联,使用 `git branch -vv` 指令可以查看仓库的相互关联
* 解决方案: `git branch --set-upstream-to=origin/远程分支的名字  本地分支的名字`

## Vue ERROR
### VueSSR造成内存溢出
* https://gitbook.cn/books/591170568b2c1f0f85f3b8fb/index.html
* https://blog.csdn.net/qq_34309704/article/details/80852449
* http://www.cnblogs.com/chuaWeb/p/5196330.html
* https://www.cnblogs.com/alongWind/p/7683624.html
* https://yq.aliyun.com/articles/682846

### Vue Router 二级路径在Nginx转发后访问不通
* Vue cli config配置中的build    assetsPublicPath: '/'

### Vue中使用axios method formData传输图片出错
* axios中添加`withCredentials: true`

```
const instance = $axios.create({
    withCredentials: true,
    headers: {
        'Content-Type':'multipart/form-data'
    }
})
```

### vue 报错 vuex requires a Promise polyfill in this browser
* 浏览器不支持es6-promise，多发生在IE浏览器中，需要添加 [es6-promise](https://github.com/stefanpenner/es6-promise#auto-polyfill) 依赖进行处理
* 解决方式：

```
1. npm install es6-promise
2. import 'es6-promise/auto' // main.js

```

### Iview中调用resetFields失效，无法重置
* 需要在form-item中添加与绑定的数据相同的prop

```
<Form inline ref="formCustom" :model="formCustom" :label-width="80">
    <FormItem label="状态" prop="status">
        <Select v-model="formCustom.status">
          <Option value="">全部</Option>
          <Option value="0">已上架</Option>
          <Option value="1">已下架</Option>
        </Select>
    </FormItem>
    <FormItem :label-width="0" prop="title">
      <Input placeholder='请输入文章标题搜索' v-model="formCustom.title"></Input>
    </FormItem>
</Form>
```
### Iview中Input标签on-change事件失效

```
// 将on-change改为@on-change，其余写法都不生效
<Input placeholder="请输入文章作者" @on-change="changeStatus()"></Input>
```
### Iview中双向绑定问题

### Iview中修改 table 表头
* 将代码中重定义的 render 改为 renderHeader 

```
// HTML
<Table :columns="columns" :data="data"></Table>

// JS
columns: [{
      type: 'selection',
      width: 60,
      align: 'center',
      fixed: 'left'
    },{
      title: '测试title',
      key: 'id',
      sortable: 'custom'
    },{
      title: '自定义表头',
      key: 'diy',
      renderHeader: (h, params) => {
        return h('div', [
          h('span', '传输状态'),
          [h('Icon', {
              props: {
                type: 'md-help-circle',
                size: '14',
              }
            })
        ])
      }
    }]
```

## Nginx ERROR
### Nginx报错
* 报错内容：`unknown directive "xxx" in /usr/local/etc/nginx/servers/hmall-shop.conf:32`
* 原因：nginx配置书写错误
* 解决方案：根据nginx报错内容，合理修改不合语法的配置，有时一个末尾的空格都会引起错误