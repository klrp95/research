# research
## git相关知识
### 1. git的简介
github存在工作区（working tree），
暂存区（index /stage），
版本库（repository）。  
1. **工作区**就是在本地的相对应的文件夹.  
2. **暂存区**英文叫index/stage。一般存放在 ".git目录下" 下的index文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。  
3. **版本库**指的是工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。



### 2. 提交、修改
* `git add <file name> ` 提交修改或新文件把文件添加到暂存区  
* `git commit -m “引号里写提交的摘要”` 把暂存区里的所有内容提交到当前分支（创建Git 版本库 时，Git自动为我们创建了唯一一个master分支)
* 如果要提交到远程仓库上，需要使用：`git push origin 当前分支`。
![imamge](https://github.com/klrp95/research/blob/master/images/0.jpeg)

### 3. 版本退回
`git log` 命令显示从最近到最远的提交日志。使用它来查看提交历史，以便确定要退回到哪个版本。  
`git log —pretty=online` 输出第一列为 commit id（版本号），是用SHA1计算出来的十六进制数字，是版本控制系统，防止冲突。第二列是提交的日志历史信息。
可以使用 `git reset —hard HEAD^`回到上一个版本（HEAD 是指向**当前版本**的**指针**，HEAD^ 是上一个版本，HEAD^^ 是上上版本，HEAD~100是前100个的版本）。  
如果退回后后悔了可以使用：  
`git reflog`来找到想要的版本id，后面跟 `git reset --hard 1094a` （版本号不用写全）来撤销相关操作。

### 4. 撤销修改
场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。  
场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。  
场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退的方法，不过前提是没有推送到远程库。

### 5. 删除文件
在正常使用`rm <file>`后，使用`git status`可以看到当前的状态  
若要撤回删除：`git checkout -- <file>`（如果文件已经提交到版本库 可以恢复）
若要彻底删除：`git rm <file>`  后面跟 `git commit -m <file>`

### 6. 分支
#### 创建、合并与删除分支
可以使用`git branch`来查看当前分支。
Git创建一个分支很快，只增加一个新指针，将HEAD的指向从之前的分支改为当前分支这两个操作。
创建+切换分支：`git checkout -b <name>`或者`git switch -c <name>`,相当于`git branch <name>` + `git checkout <name>`
合并某分支到当前分支：`git merge <name>`
删除分支：`git branch -d <name>`

#### 冲突解决
当不同分支提交了不同的修改时，Git无法自动合并分支，就必须首先解决冲突。解决冲突后，再提交，合并完成。**解决冲突**就是_把Git合并失败的文件手动编辑为我们希望的内容_，再提交。
`git log --graph --pretty=oneline --abbrev-commit`命令可以看到分支合并图。

### 7. 多人协作
使用`git clone <地址>`从远程仓库克隆时，git自动把本地 master 分支和远程的 master 分支对应起来。远程仓库默认名称是 origin。  
把当前分支上的所有本地提交推送到远程库。推送时，要指定本地分支，Git就会把该分支推送到远程库对应的远程分支上：
`git push origin master`  
如果要推送其他分支，比如dev，就改成：  
`git push origin dev`
#### 冲突解决

当多人协作，push 有冲突时：先用`git pull`把最新的提交从origin/dev抓下来，然后，在本地合并，解决冲突，再推送。

如果`git pull`显示失败，根据下面的提示`git branch --set-upstream-to=origin/dev dev`来将远程分支和本地分支关联起来。

### 8. fork
和其它的Git工作流一样，Forking工作流要先有一个公开的正式仓库存储在服务器上。 但一个新的开发者想要在项目上工作时，不是直接从正式仓库克隆，而是fork正式项目在服务器上创建一个拷贝。  
这个仓库拷贝作为他个人公开仓库 —— 其它开发者不允许`push`到这个仓库，但可以`pull`到修改（后面我们很快就会看这点很重要）。 在创建了自己服务端拷贝之后，和之前的工作流一样，开发者执行`git clone`命令克隆仓库到本地机器上，作为私有的开发环境。

要提交本地修改时，`push`提交到自己公开仓库中 —— 而不是正式仓库中。 然后，给正式仓库发起一个`pull request`，让项目维护者知道有更新已经准备好可以集成了。 

为了把功能集成到正式代码库，维护者`pull`贡献者的变更到自己的本地仓库中，检查变更以确保不会让项目出错， 合并变更到自己本地的 master 分支， 然后`push origin master`分支到服务器的正式仓库中。 到此，贡献的提交成为了项目的一部分，其它的开发者应该执行`pull`操作与正式仓库同步自己本地仓库。

## 单点登录
单点登录英文全称 Single Sign On，简称就是SSO。它的解释是：在多个应用系统中，只需要登录一次，就可以访问其他相互信任的应用系统。
### 1. web登录
账户登录和注册需要用到 Cookie 和 Session 实现的认证机制。

因为Http协议是**无状态**的，不能建立起**多次请求**之间的关系，浏览器的每一次请求，服务器会独立处理，不与之前或之后的请求产生关联。所以需要引入一个可由浏览器或服务器保存的一个**上下文状态**，也就是 __Cookie 和 Session__ 。__Session__的实现是依赖于Cookie的，因为Cookie是真正的由浏览器保存的状态，Session是利用了JSessionID。

**cookie**是一种发送到客户浏览器的**文本串句柄**，并保存在客户机硬盘上，可以用来在某个WEB站点会话间持久的保持数据。**session**指的是访问者从到达某个特定主页到离开为止的那段时间。 **Session利用Cookie进行信息处理**，当用户首先进行了请求后，服务端就在用户浏览器上创建了一个Cookie，当这个Session结束时，其实就是意味着这个Cookie就过期了。
两个都可以用来存私密的东西，同样也都有有效期的说法,区别在于session是放在服务器上的，过期与否取决于服务期的设定，cookie是存在客户端的，过去与否可以在cookie生成的时候设置进去。

例如：我们假设浏览器第一次请求服务器需要输入用户名与密码验证身份，服务器拿到用户名密码去数据库比对，正确的话说明当前持有这个session的用户是合法用户，应该将这个session标记为“已授权”或者“已登录”等等之类的状态，tomcat在会话对象中设置登录状态如下：

```
HttpSession session = request.getSession();
session.setAttribute("isLogin", true);
```
用户再次访问时，tomcat在会话对象中查看登录状态：

```
HttpSession session = request.getSession();
session.getAttribute("isLogin");
```
![imamge](https://github.com/klrp95/research/blob/master/images/1.png)
### 2.对于同一个根域下的登录问题
如果我们的站点有不止一个业务，那么他们可能部署在不同的机器上，也往往需要不同的域名进行区分。但是所有的业务又都是依赖于**一套账户体系**，那么我们这时候需要通过**一次登录**解决所有站点的登录问题，那么我们这个时候可以使用一个最笨的方法：那就是一次登录成功，将Cookie写到根域下，那么这样所有的站点就能实现，**同一个根域下的Cookie共享**，自然实现了**”单点登录“**。
### 3.对于多个根域下的登录问题
如果是多个根域名，那么这种情况下上面的机制就不能实现“单点登录”了。因为之所以上面可以实现“单点登录”的效果。是因为**浏览器和Http协议的支持**。但是对于跨根域的站点之间进行Cookie的共享是比较复杂的。

#### 方法1: 登录成功之后将Cookie回写到多个域名下（小站点）
通过后端的response写，也可以用前端js去写，但是必须有对所有需要“单点登录”的站点进行逐一的写入。但种办法是行不通的，因为你需要**维护一个站点的列表**，维护工作十分复杂，同时对于增加站点也会特别痛苦。对于Cookie的销毁也是十分复杂的，因为还是要对所有域名下的Cookie进行删除。**对于小型站点这种办法是可取的**。

#### 方法2 :引入一个中间态的Server

这种办法算是一个简化版的SSO，实现思想也十分的“狡猾”。但是对于小网站做跨域登录的处理却十分的有用，具体思路如下：

首先，我们有两个域名要实现单点登录，同时我们需要一个中间的Server。

我们有一个系统域名为xulingbo.net,当我们登录的时候访问xulingbo.net/wp-login进行登录，登录成功之后将Cookie回写到xulingbo这个域名下。  
我们还有一个系统域名为javaWeb.com，当我们访问inside-javaWeb的时候，我们没有Cookie，那么请求跳转到中间系统jump。此时需要将当前域名带到参数中便于jump校验。这个jump系统是在xulingbo域下的即：jump.xulingbo.net。这时候就能拿到之前写在xulingbo域下的Cookie。  
jump系统在收到了xulingbo域下的Cookie之后，取出xulingbo域下的Cookie，并redirect请求jump.inside-javaWeb.net,这个接口也是在jump系统中，请求后jump系统将Cookie回写到inside-javaWeb域名下，这样就实现了简易的单点登录。  
但是这种方式不是很灵活，对于数据传输的安全性没有保障，并且在销毁Cookie的时候无能为力，只能全部遍历的销毁。
![imamge](https://github.com/klrp95/research/blob/master/images/2.png)

#### 方法3 :基于CAS的SSO系统
相比于单系统登录，sso需要一个独立的认证中心，只有认证中心能接受用户的用户名密码等安全信息，其他系统不提供登录入口，只接受认证中心的间接授权。间接授权通过令牌实现，sso认证中心验证用户的用户名密码没问题，创建授权**令牌**，在接下来的跳转过程中，授权令牌作为参数发送给各个子系统，子系统拿到令牌，即得到了授权，可以借此创建局部会话，局部会话登录方式与单系统的登录方式相同。这个过程，也就是单点登录的原理，用下图说明：
![imamge](https://github.com/klrp95/research/blob/master/images/3.jpeg)
单点注销如下图所示：
![imamge](https://github.com/klrp95/research/blob/master/images/4.jpeg)

**部署图：**  
单点登录涉及**sso认证中心**与**众子系统**，子系统与sso认证中心需要通信以交换令牌、校验令牌及发起注销请求，因而子系统必须集成sso的客户端，sso认证中心则是sso服务端，整个单点登录过程实质是**sso客户端与服务端通信**的过程，用下图描述
![imamge](https://github.com/klrp95/research/blob/master/images/5.jpeg)

**实现：**  
sso采用客户端/服务端架构，我们先看sso-client与sso-server要实现的功能（下面：sso认证中心=sso-server）。

**sso-client**  
1. 拦截子系统未登录用户请求，跳转至sso认证中心；  
2. 接收并存储sso认证中心发送的令牌；  
3. 与sso-server通信，校验令牌的有效性；  
4. 建立局部会话；  
5. 拦截用户注销请求，向sso认证中心发送注销请求；  
6. 接收sso认证中心发出的注销请求，销毁局部会话。  
 
**sso-server**  
1、验证用户的登录信息；  
2、创建全局会话；  
3、创建授权令牌；  
4、与sso-client通信发送令牌；  
5、校验sso-client令牌有效性；  
6、系统注册；  
7、接收sso-client注销请求，注销所有会话。  

实现参考：<https://cloud.tencent.com/developer/article/1460801>

## 前后端分离

**单体应用结构：**  
![imamge](https://github.com/klrp95/research/blob/master/images/6.png)  
JSP 页面的开发步骤是首先需要前端工程师完成 HTML 代码，然后交给后端工程师转为 JSP 再进行开发，后端如果遇到页面问题，就需要找前端来解决，但是此时前端看到的代码已经不是他之前写的 HTML 了，是混合了一大堆标签的 JSP 代码。是一个串行的过程。

**前后端分离的结构：**  
![imamge](https://github.com/klrp95/research/blob/master/images/7.jpeg)  
前后端分离就是把原来的一个应用，拆分成两个应用。一个**纯前端应用**，专门负责数据展示和用户交互；一个**纯后端应用**，专门负责提供数据处理接口，前端 HTML 页面通过 Ajax 调用后端 RESTful API 接口进行数据交互。  
如图所示，前后端分离就是将一个应用拆成两个，前端应用和后端应用以  JSON 格式进行数据交互，这就是前后端分离的基本概念，目前最主流的实现方案是 **Spring Boot + Vue**，即后端使用 Spring Boot 框架进行开发，前端使用 Vue 框架进行开发。  
这样的话，在前后端约定接口&数据&参数后，前后端并行开发（无强依赖，可前后端并行开发，如果需求变更，只要接口&参数不变，就不用两边都修改代码，开发效率高），然后前后端集成、前端页面调整后就可以了。

使用 **Python 3 + Django 3**结合 **Vue.js** 框架构建前后端分离Web开发平台实战：
<https://cloud.tencent.com/developer/article/1576599>

**请求方式的变化：**  
以前老的方式是：  
1. 客户端请求  
2. 服务端的servlet或controller接收请求（后端控制路由与渲染页面，整个项目开发的权重大部分在后端）  
3. 调用service,dao代码完成业务逻辑  
4. 返回jsp  
5. jsp展现一些动态的代码

新的方式是：  
1. 浏览器发送请求  
2. 直接到达html页面（前端控制路由与渲染页面，整个项目开发的权重前移）  
3. html页面负责调用服务端接口产生数据（通过ajax等等，后台返回json格式数据，json数据格式因为简洁高效而取代xml）  
4. 填充html，展现动态效果，在页面上进行解析并操作DOM。  






