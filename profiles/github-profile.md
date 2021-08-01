> 不知在什么时候起，Github 支持创建同名仓库来达到美化 Github 主页的功能, 关注的很多大神也自己设计开发了自己的主页，很有特点，今天就为大家带来 Github 个性主页如何开发~

> 下方先放上个人 Github 主页 ，笔者是球迷，所以主页也是按照个人兴趣喜好来设计的，如果和您审美有差距，请直接关闭。若有帮助，请记得三联哦~

![](../images/githubProfile/github-screenshot.jpeg)

## 开发
### 创建 Github 仓库
* 在 Github 中创建与个人 ID 同名的仓库

![](../images/githubProfile/create-resp.png)

* 记得勾选下方「Add a README file」，仓库会自动创建 `readme` 文件，我们需要设计的个人主页，就需要在这个文件中进行开发。
	* `readme` 文件为 `MarkDown` 格式，开发时可以使用 `HTML+CSS` 或 `MarkDown` 语法相结合，可以实现更多的个性化样式。

![](../images/githubProfile/add-readme.png)

* 创建仓库后你就可以得到一个默认的 `Hi there` 主页信息，现在我们只需要修改 `readme` 文件就可以进行个人主页美化啦~

### 编辑 readme 文件
> 默认的 readme 文件仅仅有一些基础介绍，我相信大多数开发者都是不满足于此

* `readme` 文件使用 `MarkDown` 语法进行编写，当然你可以使用 `MarkDown` 结合 `HTML` 语言进行编写，这样生成的 `readme` 文件风格多样。

### 个性化设计
> 我们在很多大佬的 github 个人主页中经常能看到各种个性化的主页徽章或统计，那这些个性化的图表、统计、徽章都是怎么生成的，接下来我就为大家提供一些自己在开发主页过程中用(bai)到(piao)的小组件。

##### Github 徽章
![](https://img.shields.io/badge/nodeJS-1.0.1-yellowgreen) ![](https://img.shields.io/badge/VSCode-1.0.1-brightgreen)

* 大佬文章中经常使用到的这种 `badge` (徽章) ，其实都是一个个的SVG图片进行处理的，只需要通过[shields.io](https://shields.io/) 就可以自定义生成自己需要的 `badge`。
* 在网站下方的 `YOUR BBADGE` 中就能自定义 `badge`，填写相关信息之后，点击 `Make Badge` 就能获得 `badge` 的图片地址。

![](../images/githubProfile/badge.png)

##### Github 统计
##### Github Icon