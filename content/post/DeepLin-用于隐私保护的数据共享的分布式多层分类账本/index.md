---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "DeepLin 用于隐私保护的数据共享的分布式多层分类账本"
subtitle: ""
summary: "这篇文章主要讲述了一个多层区块链架构，感兴趣的同学可以移步原文哦！"
authors: [HgAmiui9]
tags: [BlockChain, Multi-Layer]
categories: [paper]
date: 2022-06-29T22:29:14+08:00
lastmod: 2022-06-29T22:29:14+08:00
featured: true
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---
**文章原文**
[DeepLinQ: Distributed Multi-Layer Ledgers for Privacy-Preserving Data Sharing](https://ieeexplore.ieee.org/document/8613656)

# 架构分析
## 优势简析
按需查询、代理任命、子组签名、细粒度访问控制、智能合约用于提高灵活性、问责制和可扩展性
## 架构设计思想
### 多层设计
多层设计：去中心化层和中心化层
适当程度的集中化与访问控制的分布式模型，最终使用智能合约来满足所有利益者相关者的要求。

 - 简仓式系统之间的共享分类账本。
 - 医院的数据库依然是可信的数据孤岛 有限数量的守信者和验证者将联合验证一个给定的合同 
 - 分布式存储
 - 数据匿名性，在数据输入时就保证不会将数据隐私数据传入分布式医疗账本上

### 工作流程

 - 紧急护理访问：非本人访问隐私数据时，需通过智能合约签署协议，且该操作将被广播至周围所有的区块链节点。 
 - 细化访问：不同级别的数据将被设置为不同的访问权限，特殊的签署协议的访问者可以随时查看该数据 如 研究机构查看医疗图片、保险公司验证付款
 - 交易费用和竞价

## 架构和组件分析
一般的分布式系统遵循 CAP 原理，而对于区块链来说，分区容忍性是必须要支持的，所以我们只能选择 AP or CP。但是在医疗保健领域，由于医院提供的集中式数据库一定是不能容忍分区性，发生分区时产生的任何访问都将被拒绝。所以，我们的系统就可以设计成同时支持可用性和一致性，作者在这提出一个观点，因为上面的观点可以使用轻量化的验证或共识模型以支持较高的交易吞吐量。

DeepLinQ 的 base layer 使用的是以太坊，其分支则由一些有特殊设计考虑的账本组成。
### branch layer 设计
为了满足隐私需求，DeepLinQ 并没有像比特币那样采用完全透明的链上传播方式，而是替换成了以下三种选择：
增加可信的高级验证者 (是否可以采用智能算法，并随着数据的不断增加，智能算法的验证精度逐渐升高)
由一组受信任的用户建立一个委员会来授予权限。这些受信任用户的数量是有限的，且控制数量保持在一定数量级即可满足可扩展性
使用子组签名
只要委员会的一个子集能够验证交易并授予权限，受信任的委员就可以采取行动，允许多重签名对电子病历进行数据访问
创建可信分支
为了减轻每个人都必须处理所有事情的情况，为特定目的建立分支。（下文再进行具体分析）
采用高效的共识协议
这里只分析了 FBA 和 hashgraph 两种共识协议，tps实验结果较高。
### 交易
![交易](https://img-blog.csdnimg.cn/img_convert/2a3b3e7e5b6f663ee0a70043ae31a6bb.png#pic_center)

将一个交易定义为一次授权的尝试。一个交易由三个字段组成。
 - initiators: 通过公钥来识别参与交易的各方 
 - actions: 一个经过加密签名的智能合约，执行一段行为的代码 
 - inecntices: 可以采用各种形式的激励措施

上述工作流程中涉及到的：

 - data owners：病人和医院，他们可以知道何时，哪些数据被收集和使用，所有者可以通过交易分配对某一数据的访问权限。
 - data guests: 业主、政府、保险、科研机构等，所有的访问都必须允许被记录，客人可以通过事务发出查询。

### 账外存储
可以使用链外存储来存储数据，只在公链上保存数据的指针，DeepLinQ可以将所有的医院数据库视为链外存储，并使用智能合约来访问链外存储。一旦合同被执行，数据被获取，就可以将其存储在一个安全的钱包中。
### 智能合约
比特币是无状态的，DeepLinQ则是有状态的。
用户通过激励的方式调用合约来消耗网络的计算能力和存储能力。
通过与智能合约的互动，用户可以证明自己EHR的存在，并管理自己与医院或者第三方的关系。主要分为三类：

 - 实体合约 EC
	 - 病人EC：记录了病人的加密数据，预先处理过的ID，身份证号和姓名的 hash... 
	 - 医院 EC：医院的名称，政府机构给的ID。
病人能够通过调用EC智能合约来识别医院的信息，反之亦然，还存在一个系统管理员
 - 关系合约 RLC：也分为病人和医院两种RLC，医院可以向病人发送一个MDC（医疗数据合同地址），病人也能够选定特定的医院分享其MDC
 - 数据合约 DC：一个医疗数据合同代表了存储在非帐薄存储中的数据。一个MDC元数据由医院ID、部门、医生姓名、时间戳、数据库名称以及记录的指纹。MDC授权用户可以通过执行写在MDC中的数据库访问代码来访问EHRs。

#### 控制访问
设置了医生和病人两个角色，实现了基于角色的访问控制。
#### 合同架构
注册、EHR 创建和 EHR 转移
### 共识协议
DeepLinQ 使用 base layer 来为branch layer 进行批量交易验证。DeepLinQ 在 branch layer 的交易组织成 Merkle 树。Merkle树是一种结构，可以有效验证大型数据体中的内容。DeepLinQ在基础层中只存储Merkle树的根签名。
使用根签名可以确保整个树在第二层是不可更改的。如果树以某种方式被篡改，存储在基础层的根签名将不符合被篡改的树根，因此，验证失败。
比特币和以太坊是轻量的，因此 base layer 区块链的延迟对整个账本系统的延迟并不是一个问题。
#### FBA
在联合拜占庭协议（FBA）系统中，每个完整的节点维护一个信任节点白名单，也被称为 a quorum slice，每个受信任的节点进一步维护自己的 quorum slice， 但是每个节点在交易结算之前都要等待其他绝大多数节点对交易的同意。重要的交易者也不会同意交易，直到他们认为重要的交易者也同意了。最终网络中足够多的参与者接受了一项交易，这样这个交易对于攻击者来说就无法攻击了，只有到那时，所有交易者才会认为该交易已经解决。FBA是无权限的，但是想要加入一个 quorum slice 只能通过邀请的方式。FBAs的共识方案可以确保金融网络的完整性。
#### hashgroup
哈希图的基本数据元素被称为八卦事件或者事件。一个事件由一个节点创建并签名，包含一个时间戳、交易、和两个哈希指针，其中一个指向父指针，另一个指向同步的节点。一个节点和它所选择的节点交换彼此未知的事件（包含最新创建的）。
每个节点记录并验证与其他节点的通信历史。通过哈希图记录了节点之间的整个通信历史。在某一时刻，两个节点可能包含不同的子集。如果两个节点都包含一个事件e和e的所有祖先，则他们享有一致性。
随着哈希图不断向上增长，不同的成员可能在靠近顶部的新事件中拥有不同的子集，但是它们将很快收敛到在哈希图的低处拥有完全相同的事件，
同步的过程中，发送的消息可能是想当大的，但是发送的数据量可能相当小，在一个节点与其他节点同步之后，它就进入了投票步骤，在哈希图共识协议中，节点不交换信息，相反，它们决定哪些事件是将通过进行虚拟投票来确定的。
对虚拟投票的相关描述：
决定回合：每个事件都被分配了一个回合编号，每轮中，每个节点的第一个事件是证人。如果一个事件e能够直接看到2/3以上的证人，那么它的轮回数就等于r+1，其中r是e的自父母和他的父母的轮回数的最大值，否则的他的轮回数就等于r。
决定著名证明：r轮的证人将对r-1轮的每个证人进行投票，以决定他们每个人是否有名。
在r-1轮中节点i的证人，在下一轮将拥有赞同票。

如果这篇文章对你有所帮助～

欢迎关注我的公众号<a href="weixin://profile/gh_02cf5e162b6d">**夏虫不可语冰也**~

未来将会更新更多区块链相关论文解读
![夏虫不可语冰也](https://img-blog.csdnimg.cn/741eff14fa2f452689981f89640be2f0.jpeg#pic_center)
