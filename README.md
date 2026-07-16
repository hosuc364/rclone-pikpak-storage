# 支持rclone的网盘怎么选？PikPak配置教程、套餐对比与10TB大空间方案全解析（含邀请码免费Premium领取）

你大概也经历过这种事：手里攥着几十上百 G 的文件，想往网盘里一塞，挂到本地当硬盘用，结果发现那网盘压根不认 rclone 这套。要么得装它家专属客户端，要么限速限到你怀疑人生，要么干脆只给开一个不能挂载的网页版。

说真的，rclone 这东西用顺手了就回不去了——一行命令把网盘当本地文件夹使，上传下载批量同步，挂载到 Windows、Mac、Linux、群晖 NAS，全平台通吃。可问题是，**不是所有网盘都愿意陪 rclone 玩**。所以"支持 rclone 的网盘有哪些、哪个值得用"这事，就成了不少人绕不开的一个问题。

这篇文章就顺着这个思路往下捋：先说 rclone 到底支持哪些网盘，再挑出其中一个特别有意思的——PikPak，手把手教你怎么用 rclone 连上它、挂载它、薅它的 10TB 空间，最后把它的全部套餐摆出来给你比一比。

## rclone 到底支持哪些网盘：先有个全局认识

rclone 官方列表里躺着的云存储后端超过 70 种，从 Amazon S3、Google Cloud Storage 这种偏开发者的对象存储，到 Google Drive、OneDrive、Dropbox、Box 这种大众网盘，再到一些"小众但好用"的选手。但这里有个关键区别——**rclone 把它们分成了 Tier 1 到 Tier 5 几个等级**，Tier 1 是核心、生产级、一等公民支持，Tier 越往后支持越弱。

这意味着什么？同样是"支持 rclone"，体验能差出十万八千里。Tier 1 的网盘，配置顺、功能全、bug 少；靠后的那些，动不动就报个错让你去论坛翻半天帖子。

下面这张表挑了几个在中文圈里被讨论最多的 rclone 网盘，给你一个直观对比：

| 网盘 | rclone 支持等级 | 免费空间 | 大容量付费价格区间 | 离线下载 | 挂载体验 |
| --- | --- | --- | --- | --- | --- |
| Google Drive | Tier 1 | 15 GB | 2TB 约$99.99/年 | 不支持 | 成熟稳定 |
| Microsoft OneDrive | Tier 1 | 5 GB | 1TB 随 Microsoft 365 | 不支持 | 稳定，偶有限流 |
| Dropbox | Tier 1 | 2 GB | 2TB 约$11.99/月 | 不支持 | 稳定 |
| pCloud | Tier 1 | 10 GB | 2TB 约$99.99/年 | 不支持 | 稳定 |
| PikPak | Tier 1 | 6 GB | 10TB 约$49.99–$100.99/年 | 支持（磁力/BT/HTTP） | 稳定，可挂载 |

看出来区别没？前面几位都是"正经网盘"的玩法——存文件、做协作，但都没有离线下载。而 PikPak 这个选手有点不一样：它从 v1.62 起就被 rclone 收进了 Tier 1 核心支持名单，配置项专门叫 `pikpak`，不光能存能挂，**服务器还能替你下种子、下磁力**。这对"想用 rclone 管理一堆下载来的大文件"的人来说，吸引力一下就不一样了。

## 为什么单独把 PikPak 拎出来讲

你可能会问，既然 Google Drive、OneDrive 这些也支持 rclone，为啥还要专门聊 PikPak？

原因很简单：**前面那些网盘解决的是"文件往哪儿放"的问题，PikPak 同时还解决了"文件从哪儿来"的问题**。

传统的流程是这样的：你先在自己电脑上挂个下载工具，跑一晚上把东西下下来，再传到网盘里，两步走，时间和宽带都花在本地。

PikPak 把这两步合成了一步。你把磁力链接、BT 种子、HTTP 直链、甚至 Telegram / Twitter 上的链接丢给它，它的服务器去下，下完直接落在你那个 10TB 的私人云盘里。然后你用 rclone 一挂，本地就能像操作硬盘一样操作那些文件——下载、上传、同步、挂载，全在 rclone 里搞定。

而且 rclone 对 PikPak 的支持不是停留在"能连上"的程度，它还给了两个 backend 专属命令：

- `rclone backend addurl`：直接在命令行里给 PikPak 丢离线下载任务
- `rclone backend decompress`：让 PikPak 在云端帮你解压 ZIP / RAR / 7z，不用先把整个压缩包拉回本地

这俩功能在别的网盘后端里可不多见。

## 用 rclone 连接 PikPak：照着敲就行

这部分别怕，真不难。rclone 的配置全程是交互式的，它问你答，一路回车就完事。

**第一步：装好 rclone，进配置**

装 rclone 这步不展开（Windows / Mac / Linux 官网都有现成包）。装完之后在命令行敲：

bash
rclone config


**第二步：新建一个远程**

它问你 `n/s/q`，输入 `n` 表示新建。然后让你给这个远程起个名，建议用英文，带个 `pikpak` 便于认，比如 `my-pikpak`。

**第三步：选存储类型**

它会列一长串支持的云盘，每个前面有编号。找到写着 `PikPak` 的那一行，输入对应编号。

**第四步：填账号密码**

这里有个坑要提醒你：rclone 让你填的"user"，**不是 PikPak 网站上那个昵称用户名，而是你注册时用的邮箱，或者带区号的手机号**（比如 `+86138xxxx`）。填错了会连不上，很多人卡在这一步。

密码那步它问你 `y/g`，输 `y` 自己手动输两遍密码就行。

**第五步：高级配置和保存**

问你 `Edit advanced config?`，直接回车跳过。最后它会显示一段 token 信息，问你 `Keep this remote?`，回车确认（默认 `y`），完事。

连上之后，你就能这么用：

bash
# 把 PikPak 里 My Pack 目录下的某个文件下到本地
rclone copy -P my-pikpak:'My Pack'/my-video.mp4 .

# 把本地文件传上去
rclone copy -P ./my-video.mp4 my-pikpak:'My Pack'/


注意 PikPak 默认有个叫 `My Pack` 的目录（中间带空格），命令行里记得用引号包起来，不然空格会被 shell 吃掉。

## 进阶玩法：挂载、WebDAV 和离线下载

配置好只是起点，真正让 rclone + PikPak 这套组合"香"起来的，是下面这几个玩法。

**把 PikPak 挂成本地硬盘**

用 `rclone mount` 可以把 PikPak 整个挂到你本地的某个目录，挂完之后它就是一个看起来和 C 盘 D 盘没区别的盘符。在群晖 NAS 上，很多人就是靠这套把 PikPak 当扩展存储用——NAS 本身硬盘不够大，挂个 10TB 的 PikPak 上去，瞬间扩容。

**用 rclone serve 起 WebDAV**

PikPak 自己其实有个官方 WebDAV，但说实话不太完整：只读、只给 Premium 会员、藏在"实验室功能"里。更靠谱的做法是让 rclone 当中间层，用 `rclone serve webdav` 把 PikPak 转成一个标准的 WebDAV 服务，这样任何支持 WebDAV 的文件管理器、媒体播放器（Infuse、NPlayer 之类）都能直接连。

**命令行直接丢离线下载任务**

前面提到的 `addurl` 命令，实战长这样：

bash
rclone backend addurl my-pikpak:'My Pack' https://example.com/file.zip


丢完就走人，PikPak 服务器在云端慢慢下，下完自动进你的盘。配合 Telegram 机器人（PikPak 有个官方 Bot，转发链接给它就能转存），下载这件事基本可以做到"全自动"。

## PikPak 全套餐对比：一张表看明白

PikPak 的套餐结构比一般网盘稍微复杂一点，因为它从 2023 年 7 月起把 Premium 拆成了两类：**Global Premium**（面向 23 个发达国家和地区）和 **Regional Premium**（面向其他地区），每类都有月付和年付。它会在结账时根据你的 IP、设备语言、SIM 卡国家码自动判断你属于哪一类，不用你自己猜。

下面这张表覆盖了官网在售的全部方案：

| 套餐 | 存储空间 | 下载速度 | 计费周期 | 价格 | 购买入口 |
| --- | --- | --- | --- | --- | --- |
| Free 免费版 | 6 GB | 共享带宽 | 永久免费 | $0 |  [注册领取](https://bit.ly/PIkpak) |
| Regional Premium 月付 | 10 TB | 最高 8 MB/s | 按月 | 约 $5.79–$8.09/月（按地区） |  [前往订阅](https://mypikpak.com/drive/payment?invitation-code=74098243) |
| Regional Premium 年付 | 10 TB | 最高 8 MB/s | 按年 | 约 $57.59–$63.99/年（按地区） |  [前往订阅](https://mypikpak.com/drive/payment?invitation-code=74098243) |
| Global Premium 月付 | 10 TB | 最高 20 MB/s，无地域限制 | 按月 | 约 $7.99–$10/月（按地区） |  [前往订阅](https://mypikpak.com/drive/payment?invitation-code=74098243) |
| Global Premium 年付 | 10 TB | 最高 20 MB/s，无地域限制 | 按年 | 约 $49.99–$100.99/年（按地区） |  [前往订阅](https://mypikpak.com/drive/payment?invitation-code=74098243) |

> 说明：PikPak 的最终价格是结账时根据你所在地区动态显示的，上表区间是综合官方支付页与各渠道公开信息整理的范围，以你实际看到的为准。PikPak 的所有套餐购买都走统一的订阅页面，进入后选择对应周期和类型即可。

## Global 和 Regional 怎么选，邀请码怎么用

**两类 Premium 的核心区别**就两句话：Global 给你最高 20 MB/s 的下载速度，而且你跑到哪都不限速；Regional 给你最高 8 MB/s，平时够用，但你要是跑去那 23 个发达国家，速度会被压，还会弹窗让你升级 Global。

价格上 Global 明显贵一截，但官方的说法是 Regional 是他们主动压价、甚至压缩利润来让更多发展中国家用得起的——所以如果你人在国内或者东南亚、拉美这些地方，Regional 年付其实是性价比最高的那个档。

**关于邀请码**：你手上这个邀请码 `74098243`，在注册的时候填进去，有机会拿到免费的 Premium 会员体验时长。注册入口已经把邀请码带在链接里了，你不用再手动复制粘贴，点进去注册就行：

👉 [用邀请码注册 PikPak，拿免费 Premium 体验](https://bit.ly/PIkpak)

另外提一句，PikPak 还有个兑换码（Redeem Code）机制，在 Premium 购买页右上角有个"Redeem"按钮可以输。但要注意：Global 地区不能拿 Regional 的兑换码去兑，反过来 Regional 地区两种码都能用。如果你手上拿到的是第三方兑换码，先看清楚它是哪一类。

## PikPak 跟其他 rclone 网盘比，到底强在哪弱在哪

说了这么多 PikPak 的好，也得把话说全。它不是万能的，挑场景。

| 维度 | PikPak Premium | Google Drive | OneDrive |
| --- | --- | --- | --- |
| 存储空间 | 10 TB | 2 TB（同价位） | 1 TB（随 M365） |
| 服务器端离线下载 | 有，磁力/BT/HTTP | 没有 | 没有 |
| rclone 挂载 | Tier 1，稳定 | Tier 1，稳定 | Tier 1，偶有限流 |
| 4K 云端串流 | 支持 | 有限 | 有限 |
| 文档协作编辑 | 不擅长 | 强 | 强（Office 全家桶） |
| 同等空间年费 | 约 $50–$100 | 约 $100（2TB） | 随 Microsoft 365 |

简单讲：**如果你的需求是"下大文件、存大文件、挂载起来随时调取、在线看片"，PikPak 在 rclone 网盘里几乎没有对手**，10TB 的起步空间和服务器端离线下载这两个特性叠在一起，传统网盘给不了。

但如果你要的是团队协作、在线编辑 Word / Excel、共享文件夹给同事一起改，那 PikPak 不是干这个的，Google Drive 或者 OneDrive 才是正解。

## 这套方案到底适合谁

写到这，可以把话说得具体一点了。

- **经常下种子、磁力的人**：PikPak 的服务器替你下，rclone 替你管，你不用再开电脑挂一晚上。这是它最对口的人群。
- **NAS 玩家、想给设备扩容的**：群晖、unRAID、OpenWRT 路由器上跑 rclone 挂 PikPak，10TB 当扩展盘使，成本比再买几块硬盘低得多。
- **追剧党、影音收藏控**：下完直接在云端 4K 播放，不用先拉回本地，配合 rclone serve WebDAV 还能接各种播放器。
- **重度 Telegram 用户**：PikPak 有官方 Bot，转发链接即转存，rclone 再做本地同步，一套全自动的"云端收件箱"。
- **只想拿 rclone 做正经同步备份的**：那其实 Google Drive / OneDrive 也够用，PikPak 的离线下载优势你用不上，但它 10TB 的空间单价还是香的。

## 收尾几句

"支持 rclone 的网盘"这个问题的答案其实不少，但如果你要的是一个**既被 rclone 当一等公民对待、又能替你下东西、还给 10TB 空间**的方案，PikPak 是目前最对路的那一个。rclone v1.62 起原生内置它的后端，配置就是填个邮箱密码的事，挂载、WebDAV、离线下载、云解压都能在命令行里跑通。

想先试试水的话，用邀请码注册一个免费账号，6GB 空间够你把整套流程跑一遍——挂载、下载、串流都试一遍，觉得顺手了再上 Premium，年付是最划算的档：

👉 [注册 PikPak 免费账号（邀请码已内置）](https://bit.ly/PIkpak)

👉 [查看 Premium 套餐并订阅](https://mypikpak.com/drive/payment?invitation-code=74098243)

rclone 这东西，用一次就离不开；PikPak 这套组合，用顺了你会发现，以前那个"先下再传"的老办法，确实可以退休了。
