---
title: 关于变更评审的进一步思考
date: 2023-04-25 15:28:22
keywords: 运维变更, 运维变更评审, 变更评审
description: 本文继续写下一些对于运维变更评审工作的一些想法，包括是否引入业务用户、评审的分类要点、评审的形式等等，欢迎同行参与讨论。
---

## 变更评审的意义

评审从字面上理解主要是对变更方案合理性、风险和影响的评估，变更评审应当根据组织的特点来采取针对性的形式和流程。通过变更评审过程，可以增加变更实施人员对于变更内容和影响的理解，降低变更带来的风险，提高变更问题的处理恢复速度。

每一个项变更的提出，都有其特定的业务背景，做为变更发起人在提交变更评审的时候，需要把事项的背景介绍清楚，以便于评审人员能够把握评审的标准。

如果在一个人数较少的组织，如自由创业者或者3～5人的创业团队，每个人都应该对变更的内容负责任，这种组织下，变更评审的意义就不是很大。

对于超过5人的团队，如果想控制变更的质量，并且做到每一项变更都能够事后追溯，那就需要制定变更流程、组织变更评审。

## 运维如何高质量交付环境

信息系统运行需要一定的环境和资源，包括设施环境（风火水电）、网络、服务器等等。

从最宏观的角度来考虑信息系统的安全稳定运行，首先要解决的就是选址问题，2021年7月20日，郑州遭遇罕见的大暴雨，部分IDC机房收到直接冲击。还有一部分机房，因为电力供应受损，柴油又因为暴雨原因无法及时送达而影响了业务。

设施环境的选择不仅仅是对于大型数据中心和企业来说很重要，对于小型企业来说也非常重要。俗话说「不要把鸡蛋放在一个篮子里」，在选择购买服务的的时候，考虑一个备用的环境，就能给自己在极端情况下留好后路。

在准备服务器环境方面，涉及到的细节要素就会更多。服务器型号和配置要求、网络带宽要求、操作系统参数要求、文件系统要求、操作系统用户管理与分离要求、监控部署要求。做运维，并不是把服务器买来交给开发使用就算完成了。在运维的整个生命周期里，我们要负责系统的安全稳定运行，在发生紧急故障的时候，能够捋清楚数据流向、业务流向等等影响应急决策关键信息。这就要求我们建立起组织级别的、分层的规范来帮助我们提高管理的有效性。

## 是否引入业务用户

观察 ITIL 的资料，变更管理委员会（Change Advisory Board，CAB）是为了帮助评估变更而召集的组织，建议的成员包括了业务用户（特别是业务负责人），现在就想讨论下在变更评审中引入业务用户的必要性和实现难度。

一个系统的变更，从与业务用户是否相关的角度来说，一类是有业务变化或影响的变更，如版本投产上线新功能，或者在业务运行时段却需要做一些维护性动作（如重启、设备更换等等），这种情况下应当请业务用户从业务运行稳定的角度来参与变更评估。另一类是理论上应该完全没有业务影响的变更，例如集群中某个节点的故障维修、磁盘更换等硬件类问题，这种类型的变更，我觉得可以不用事无巨细的通知业务用户。



## 参考资料

1. [ITSS变更管理的风险和影响评估](https://www.itilxf.com/thread-52356-1-1.html)
2. [数据中心因暴雨崩溃，应急处置如何做](https://baijiahao.baidu.com/s?id=1705983883408379028&wfr=spider&for=pc)
3. [从运维管理角度看变更](https://cloud.tencent.com/developer/article/1896279)
