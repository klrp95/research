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

1、 sso-client拦截未登录请求  
java拦截请求的方式有servlet、filter、listener三种方式，我们采用filter。在sso-client中新建LoginFilter.java类并实现Filter接口，在doFilter()方法中加入对未登录用户的拦截。

```
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
    HttpServletRequest req = (HttpServletRequest) request;
    HttpServletResponse res = (HttpServletResponse) response;
    HttpSession session = req.getSession();

    if (session.getAttribute("isLogin")) {
        chain.doFilter(request, response);
        return;
    }
    //跳转至sso认证中心
    res.sendRedirect("sso-server-url-with-system-url");
}
```
2、sso-server拦截未登录请求

拦截从sso-client跳转至sso认证中心的未登录请求，跳转至登录页面，这个过程与sso-client完全一样。

3、sso-server验证用户登录信息

用户在登录页面输入用户名密码，请求登录，sso认证中心校验用户信息，校验成功，将会话状态标记为“已登录”。

```
@RequestMapping("/login")
public String login(String username, String password, HttpServletRequest req) {
    this.checkLoginInfo(username, password);
    req.getSession().setAttribute("isLogin", true);
    return "success";
}
```
4、sso-server创建授权令牌

授权令牌是一串随机字符，以什么样的方式生成都没有关系，只要不重复、不易伪造即可，下面是一个例子:  
```
String token = UUID.randomUUID().toString();
```
5、sso-client取得令牌并校验

sso认证中心登录后，跳转回子系统并附上令牌，子系统（sso-client）取得令牌，然后去sso认证中心校验，在LoginFilter.java的doFilter()中添加几行：

```
// 请求附带token参数
String token = req.getParameter("token");
if (token != null) {
    // 去sso认证中心校验token
    boolean verifyResult = this.verify("sso-server-verify-url", token);
    if (!verifyResult) {
        res.sendRedirect("sso-server-url");
        return;
    }
    chain.doFilter(request, response);
}
```
verify() 方法使用httpClient实现，这里仅简略介绍，httpClient详细使用方法请参考官方文档。

```
HttpPost httpPost = new HttpPost("sso-server-verify-url-with-token");
HttpResponse httpResponse = httpClient.execute(httpPost);
```

6、sso-server接收并处理校验令牌请求

用户在sso认证中心登录成功后，sso-server创建授权令牌并存储该令牌，所以，**sso-server对令牌的校验就是去查找这个令牌是否存在以及是否过期**，令牌**校验成功**后sso-server将发送校验请求的系统注册到sso认证中心（就是存储起来的意思）。

令牌与注册系统地址通常存储在 **key-value数据库（如redis）**中，redis可以为key设置**有效时间**也就是令牌的有效期。redis运行在**内存**中，速度非常快，正好sso-server不需要持久化任何数据。

令牌与注册系统地址可以用下图描述的结构存储在redis中。如果不存储，注销的时候就麻烦了，用户向sso认证中心提交注销请求，sso认证中心注销全局会话，但不知道哪些系统用此全局会话建立了自己的局部会话，也不知道要向哪些子系统发送注销请求注销局部会话。  
![imamge](https://github.com/klrp95/research/blob/master/images/8.png) 

7、sso-client校验令牌成功创建局部会话

令牌校验成功后，sso-client将当前局部会话标记为“已登录”，修改LoginFilter.java，添加几行：

```
if (verifyResult) {
    session.setAttribute("isLogin", true);
}
```
sso-client还需将**当前会话id与令牌绑定**，表示这个会话的登录状态与令牌相关，此关系可以用java的**hashmap**保存，保存的数据用来处理sso认证中心发来的注销请求。

8、注销过程

用户向子系统发送带有“logout”参数的请求（注销请求），sso-client拦截器拦截该请求，向sso认证中心发起注销请求

```
String logout = req.getParameter("logout");
if (logout != null) {
    this.ssoServer.logout(token);
}
```

sso认证中心也用同样的方式识别出sso-client的请求是注销请求（带有“logout”参数），sso认证中心注销全局会话

```
@RequestMapping("/logout")
public String logout(HttpServletRequest req) {
    HttpSession session = req.getSession();
    if (session != null) {
        session.invalidate();//触发LogoutListener
    }
    return "redirect:/";
}
```
sso认证中心有一个全局会话的监听器，一旦全局会话注销，将通知所有注册系统注销

```
public class LogoutListener implements HttpSessionListener {
    @Override
    public void sessionCreated(HttpSessionEvent event) {}
    @Override
    public void sessionDestroyed(HttpSessionEvent event) {
        //通过httpClient向所有注册系统发送注销请求
    }
```
## 前后端分离

**单体应用结构：**  
![imamge](https://github.com/klrp95/research/blob/master/images/6.png)  
JSP 页面的开发步骤是首先需要前端工程师完成 HTML 代码，然后交给后端工程师转为 JSP 再进行开发，后端如果遇到页面问题，就需要找前端来解决，但是此时前端看到的代码已经不是他之前写的 HTML 了，是混合了一大堆标签的 JSP 代码。是一个串行的过程。
![imamge](https://github.com/klrp95/research/blob/master/images/9.jpeg)
用户在浏览器上发送请求，服务器端接收到请求，根据 Header 中的 token 进行用户鉴权，从数据库取出数据，处理后将结果数据填入 HTML 模板，返回给浏览器，浏览器将 HTML 展现给用户。
   
**前后端分离的结构：**  
![imamge](https://github.com/klrp95/research/blob/master/images/7.jpeg)  
前后端分离就是把原来的一个应用，拆分成两个应用。一个**纯前端应用**，专门负责数据展示和用户交互；一个**纯后端应用**，专门负责提供数据处理接口，前端 HTML 页面通过 Ajax 调用后端 RESTful API 接口进行数据交互。  
如图所示，前后端分离就是将一个应用拆成两个，前端应用和后端应用以  JSON 格式进行数据交互，这就是前后端分离的基本概念，目前最主流的实现方案是 **Spring Boot + Vue**，即后端使用 Spring Boot 框架进行开发，前端使用 Vue 框架进行开发。  
这样的话，在前后端约定接口&数据&参数后，前后端并行开发（无强依赖，可前后端并行开发，如果需求变更，只要接口&参数不变，就不用两边都修改代码，开发效率高），然后前后端集成、前端页面调整后就可以了。  
![imamge](https://github.com/klrp95/research/blob/master/images/10.jpeg)

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

## 微服务
微服务(Microservices Architecture)是一种架构风格，一个大型复杂软件应用由一个或多个微服务组成。系统中的各个微服务可被独立部署，各个微服务之间是松耦合的。每个微服务仅关注于完成一件任务并很好地完成该任务。在所有情况下，每个任务代表着一个小的业务能力。
### 单体架构
企业级的应用一般都会面临各种各样的业务需求，而常见的方式是把大量功能堆积到同一个单体架构中去。比如：常见的ERP、CRM等系统都以单体架构的方式运行，同时由于提供了大量的业务功能，随着功能的升级，整个研发、发布、定位问题，扩展，升级系统会变得越来越困难。  

这种架构模式就是把应用**整体**打包部署，具体的样式依赖本身应用采用的语言，如果采用java语言，自然你会打包成war包，部署在Tomcat或者Jetty这样的应用服务器上，如果你使用spring boot还可以打包成jar包部署。其他还有Rails和Node.js应用以目录层次的形式打包。

大部分企业通过**SOA**来解决上述问题，SOA的思路是把应用中相近的功能聚合到一起，以服务的形式提供出去。因此基于SOA架构的应用可以理解为一批服务的组合。SOA带来的问题是，引入了大量的服务、消息格式定义和规范。多数情况下，SOA的服务直接相互独立，但是部署在同一个运行环境中（类似于一个Tomcat实例下，运行了很多web应用）。和单体架构类似，随着业务功能的增多SOA的服务会变得越来越复杂，本质上看没有因为使用SOA而变的更好。

**单体架构的应用一般有以下特点：**

* 设计、开发、部署为一个单独的单元。
* 会变得越来越复杂，最后导致维护、升级、新增功能变得异常困难
* 很难以敏捷研发模式进行开发和发布
* 部分更新，都需要重新部署整个应用水平扩展：必须以应用为单位进行扩展，在资源需求有冲突时扩展变得比较困难（部分服务需要更多的计算资源，部分需要更多内存资源）
* 可用性：一个服务的不稳定会导致整个应用出问题
* 创新困难：很难引入新的技术和框架，所有的功能都构建在同质的框架之上
* 运维困难：变更或升级的影响分析困难，任何一个小修改都可能导致单体应用整体运行出现故障。

### 微服务架构
微服务架构的**核心思想**是，一个应用是由多个小的、相互独立的、微服务组成，这些服务运行在自己的进程中，开发和发布都**没有依赖**。不同服务通过一些**轻量级交互机制**来通信，例如 RPC、HTTP 等，服务可独立扩展伸缩，每个服务定义了明确的边界，不同的服务甚至可以采用不同的编程语言来实现，由独立的团队来维护。简单的来说，一个系统的不同模块转变成不同的服务！而且服务可以使用不同的技术加以实现！

### 微服务的设计指南
* 职责单一原则（Single Responsibility Principle）：把某一个微服务的功能聚焦在特定业务或者有限的范围内会有助于敏捷开发和服务的发布。
* 设计阶段就需要把业务范围进行界定。
* 需要关心微服务的业务范围，而不是服务的数量和规模尽量小。
* 数量和规模需要依照业务功能而定。
* 于SOA不同，某个微服务的功能、操作和消息协议尽量简单。
* 项目初期把服务的范围制定相对宽泛，随着深入，进一步重构服务，细分微服务是个很好的做法。

### 微服务消息
**同步消息 REST**  
同步消息就是客户端需要保持等待，直到服务器返回应答。REST是微服务中默认的**同步消息方式**，它提供了基于HTTP协议和资源API风格的简单消息格式，多数微服务都采用这种方式（每个功能代表了一个资源和对应的操作）。

**异步消息 – AMQP, STOMP, MQTT**  
异步消息就是客户端不需要一直等待服务应答，有应答后会得到通知。某些微服务需要用到异步消息，一般采用AMQP, STOMP, MQTT 这三种通讯协议。

**消息格式 – JSON, XML, Thrift, ProtoBuf, Avro**  
消息格式是微服务中另外一个很重要的因素。微服务采用简单的文本协议JSON和XML，基于HTTP的资源API风格。如果需要二进制，通过用到Thrift, ProtoBuf, Avro。

**服务约定 – 定义接口 – Swagger, RAML, Thrift IDL**  
如果把功能实现为服务，并发布，需要定义一套约定。REST设计的微服务，通常采用Swagger和RAML定义约定。对于不是基于REST设计的微服务，比如Thrift，通常采用IDL（Interface Definition Languages），比如Thrift IDL。
### 微服务集成 (服务间通信)
大部分微服务基于RPC、HTTP、JSON这样的标准协议，集成不同标准和格式变的不再重要。另外一个选择是采用轻量级的消息总线或者网关，有路由功能，没有复杂的业务逻辑。

**点对点方式**  
![imamge](https://github.com/klrp95/research/blob/master/images/11.png)  
点对点方式中，服务之间直接用。每个微服务都开放 REST API，并且调用其它微服务的接口。很明显，在比较简单的微服务应用场景下，这种方式还可行，随着应用复杂度的提升，会变得越来越不可维护。

**API-网关方式**  
![imamge](https://github.com/klrp95/research/blob/master/images/12.png)   
所有的业务接口通过**API网关**暴露，是所有客户端接口的唯一入口。微服务之间的通信也通过API网关。  

采用网关方式有如下优势：

* 有能力为微服务接口提供网关层次的抽象。比如：微服务的接口可以各种各样，在网关层，可以对外暴露统一的规范接口。
* 轻量的消息路由、格式转换。统一控制安全、监控、限流等非业务功能。
* 每个微服务会变得更加轻量，非业务功能个都在网关层统一处理，微服务只需要关注业务逻辑目前，API网关方式应该是微服务架构中应用最广泛的设计模式。

**消息代理方式**  
微服务也可以集成在**异步**的场景下，通过队列和订阅主题，实现消息的发布和订阅。一个微服务可以是消息的发布者，把消息通过异步的方式发送到队列或者订阅主题下。作为消费者的微服务可以从队列或者主题共获取消息。通过消息中间件把服务之间的直接调用解耦。  
![imamge](https://github.com/klrp95/research/blob/master/images/13.png)   
通常异步的生产者/消费者模式，通过AMQP, STOMP, MQTT 等异步消息通讯协议规范。
### 数据的去中心化
单体架构中，不同功能的服务模块都把数据存储在某个中心数据库中。  
![imamge](https://github.com/klrp95/research/blob/master/images/14.png)   
每个微服务有自己私有的数据库，其它微服务不能直接访问。单体架构，用一个数据库存储所有数据。

微服务方式，多个服务之间的设计相互独立，数据也应该相互独立（比如，某个微服务的数据库结构定义方式改变，可能会中断其它服务）。因此，**每个微服务都应该有自己的数据库**。  
![imamge](https://github.com/klrp95/research/blob/master/images/15.png)   
每个微服务有自己私有的数据库，其它微服务不能直接访问。  
数据的去中心化，进一步降低了微服务之间的耦合度，不同服务可以采用不同的数据库技术（SQL、NoSQL等）。在复杂的业务场景下，如果包含多个微服务，通常在客户端或者中间层（网关）处理。

参考资料：<https://juejin.im/post/5cc7048ce51d456e811d2732>


















