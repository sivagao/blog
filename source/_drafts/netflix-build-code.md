在网飞我们是如何构建代码的


在通过网络部署上云之前，我们网飞是如何编译代码？考虑到之前部分内容已经被讲诉过，这次我们准备分享更多细节。在这篇文章中，我们描述工具和技术用来从源代码到最总部署为线上服务来给全球超过7500w的用户提供电影和电视节目的服务

上述图标对之前博客讲的Spinnaker做了扩展，我们全球的持续交付的平台。如果一行代码需要进入Spinnaker中，这之间还有一系列的步骤用来执行。

- Code is built and tested locally using Nebula
- Changes are committed to a central git repository
- A Jenkins job executes Nebula, which builds, tests, and packages the application for deployment
- Builds are “baked” into Amazon Machine Images
- Spinnaker pipelines are used to deploy and promote the code change

这篇文章接下来就探索用在上述每个步骤中的工具和流程，也解释了我们为什么这么做的原因。最后通过分享我们现在正在面临的难题结尾。你可以认为这是我们准备详细讲诉在网飞我们如何构建和发布代码的如工具和挑战等方方面面系列文章的第一篇。

### 文化，云和微服务

### 构建

### 整合

### 部署

### 前路挑战



