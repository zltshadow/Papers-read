# 论文5-联邦学习系统的调查

原文：[联邦学习系统的调查:数据隐私和保护的愿景、炒作和现实](https://arxiv.org/abs/1907.09693)

## 摘要

联邦学习在实现不同组织间机器学习模型在隐私约束下的协同训练方面一直是一个热门研究课题。随着研究人员试图用不同的隐私保护方法支持更多的机器学习模型，在开发系统和基础设施时需要简化各种联邦学习算法的开发。与深度学习系统\(如PyTorch和TensorFlow\)类似，联邦学习系统\(fls\)同样重要，并面临来自有效性、效率和隐私等各个方面的挑战。在这次调查中，我们对联邦学习系统进行了全面的回顾。为了实现顺畅的流程并指导未来的研究，我们引入了联邦学习系统的定义并分析了系统的组成部分。此外，我们还从数据分布、机器学习模型、隐私机制、通信架构、联邦规模和联邦动机等6个方面对联邦学习系统进行了全面的分类。如我们的案例研究中所示，分类可以帮助设计联邦学习系统。通过系统总结现有的联邦学习系统，我们提出了设计因素、案例研究和未来的研究机会。

## 1 引言

许多机器学习算法都需要大量数据，而实际上，在隐私限制的保护下，数据分散在不同的组织中。由于这些因素，联邦学习\(FL\)\[146， 225， 100\]已经成为机器学习的一个热门研究课题。例如，不同医院的数据相互隔离，成为“数据孤岛”。由于每个数据岛在规模和近似真实分布方面都有限制，单个医院可能无法训练出对特定任务具有良好预测精度的高质量模型。理想情况下，如果医院能够联合它们的数据，协同训练一个机器学习模型，它们将受益更多。然而，由于各种政策和法规，这些数据不能简单地在医院之间共享。这种“数据孤岛”的现象在金融、政府、供应链等许多领域都很常见。《通用数据保护条例》\(GDPR\)\[11\]等政策规定了不同组织之间的数据共享规则。因此，在遵循政策法规保护隐私的同时，开发一个具有良好预测准确性的联邦学习系统是一项具有挑战性的工作。

最近，许多人致力于实现联邦学习算法，以支持有效的机器学习模型。具体来说，研究人员试图通过不同的隐私保护方法支持更多的机器学习模型，包括深度神经网络\(NNs\)\[136，233，26，176， 146\]，梯度增强决策树\(gbdt\)\[237，46，120\]，逻辑回归\[159，43\]和支持向量机\(svm\)\[188\]。例如，Nikolaenko等\[159\]和Chen等人\[43\]提出采用基于线性回归的方法进行FL。Hardy等人\[87\]实现了一个FL框架来训练逻辑回归模型。由于gbdt近年来非常成功\[40，218\]，赵等人\[237\]，Cheng等人\[46\]，Li等人\[120\]也提出了相应的联邦学习系统\(Federated Learning Systems， FLSs\)。另一种流行的决策树集成方法，如随机森林，也被扩展到支持隐私保护\[170\]，这是支持FL的重要一步。此外，有许多基于神经网络的fls。谷歌提出了一种可扩展的生产系统，可以使数千万设备训练深度神经网络\[26\]。Y urochkin等人\[233\]利用贝叶斯非参数机制开发了神经网络的概率FL框架。有几种方法尝试将外语学习与机器学习技术相结合，如多任务学习和迁移学习。Smith等人\[188\]将FL与多任务学习结合起来，允许多方完成单独的任务。为了解决标签信息只存在于一方的情况，Yang等人\[225\]采用迁移学习来协作学习模型。

在联邦环境下自定义机器学习算法的研究中，我们已经确定了一些常用的方法和方法。以提供隐私保障的方法为例。一种常见的方法是使用加密技术\[25\]，如安全多方计算\[151\]和同态加密\[87\]。另一种流行的方法是差分隐私\[237\]，它在模型参数中添加噪声以保护个人记录。例如，谷歌的FLS\[25\]同时采用了安全聚合和差异隐私保护，增强了隐私保护。

由于构建FL算法有常见的方法和构建块，因此开发系统和基础设施来简化各种FL算法的开发是有意义的。系统和基础设施允许算法开发人员重用公共构建块，避免每次从头构建算法。类似于深度学习系统，如PyTorch\(166、167\)和TensorFlow\[8\]，促进深度学习算法的发展，FLSs对FL的成功同样重要。然而，建设一个成功的FLS的挑战，这需要考虑多个方面如有效性、效率、隐私和自主权。

在本文中，我们从系统的角度对现有的工作流系统进行了综述。首先，我们展示FLSs的定义，并将其与传统联邦系统进行比较。其次，分析了系统的组成部分，包括系统的参与者、管理人员和计算通信框架。第三，从数据分布、机器学习模型、隐私机制、通信架构、联邦规模和联邦动机六个方面对fls进行了分类。这些方面可以指导作为公共构建块和系统抽象的FLS的设计。第四，在这些方面的基础上，系统总结了现有的研究成果，可以用于指导FLSs的设计。最后，为了使FL更加实用和强大，我们提出了未来的研究方向。我们相信系统和基础设施对于FL的成功至关重要。在有效性、效率、隐私和自治方面，还需要进行更多的工作来解决系统研究问题。

### 1.1 相关调查

有一些关于滤过性滤过性的调查。Yang等人\[225\]撰写的一份开创性的调查报告介绍了FL的基础和概念，并进一步提出了一个全面的安全FL框架。后来微众银行\[215\]发表了白皮书，介绍了FL的背景和相关工作，最重要的是提出了建立本地和全球标准、构建用例、形成行业数据联盟等发展路线图。本文主要针对相对较少的一方，通常是企业数据所有者。Li等人\[124\]总结了FL在移动和边缘设备大规模网络中的挑战和未来发展方向。最近，Kaiouz等\[100\]从不同的研究课题对FL的特点和挑战进行了全面的描述。然而，他们主要关注跨设备FL，其中的参与者是大量的移动或物联网设备。最近，另一项调查\[12\]总结了联邦学习的平台、协议和应用。一些调查只关注联邦学习的一个方面。例如，Lim等人\[129\]针对移动边缘计算进行了一项FL调查，而\[142\]主要关注联邦学习面临的威胁。

### 1.2 我们的贡献

据我们所知，目前缺乏对FLSs现有系统和基础设施的回顾，也缺乏对创建FLSs的关注\(类似于深度学习领域蓬勃发展的系统研究\)。与以往的研究相比，本文的主要贡献如下。\(1\)我们的调查是第一次从系统的角度对FL进行全面的分析，包括系统组成、分类、总结、设计和愿景。\(2\)我们从数据分布、机器学习模型、隐私机制、通信体系结构、联邦规模、联邦动机等6个方面对fls进行了综合分类，可作为fls的公共构建块和系统抽象。\(3\)我们对现有的典型研究和最新研究成果按研究领域进行总结，方便研究人员和开发人员参考。\(4\)我们提出了成功的FLS的设计因素，并对每个场景进行了全面的审查。\(5\)我们提出了未来FLSs的研究方向和挑战。本文的其余部分组织如下。

在第2节中，我们将介绍FLS的概念，并将其与传统联邦系统进行比较。在第3节中，我们介绍了FLS的系统组成部分。在第4节中，我们提出了从6个方面来分类fls。在第5节中，我们总结了关于FL的现有研究和系统，然后在第6节中介绍了FL的设计因素和解决方案。最后，我们在第8节中提出未来可能的FL方向，并在第9节中总结我们的论文。

## 2 联邦学习系统概述

### 2.1 背景

近年来，数据泄露严重威胁着用户的数据隐私。在2019年的一次重大泄露中，超过5.4亿Facebook用户的记录被泄露在亚马逊的云\[3\]上。2019年，美国海关和边境保护局宣布了一项数据泄露事件，数万张旅行者的照片被泄露。

随着数据泄露成为人们关注的主要问题，越来越多的政府建立了保护用户数据的法规，如欧盟的GDPR\[203\]，新加坡的PDPA\[47\]，美国的CCPA\[1\]。对于公司来说，违反这些政策的代价是相当高的。2016年，60万名司机的个人信息被泄露，优步不得不支付1.48亿美元以了结调查。SingHealth因违反PDPA\[5\]被新加坡政府罚款75万美元。谷歌因违反GDPR\[4\]被罚款5700万美元，这是截至2020年3月18日根据欧盟隐私法开出的最高罚单。

在上述情况下，联邦学习，即不交换用户原始数据的协作学习，越来越受到人们的关注。机器学习，特别是深度学习，近年来再次受到人们的关注，而联邦与机器学习的结合则是一个新的研究热点。

### 2.2 定义

FL使多方在不交换本地数据的情况下共同训练机器学习模型。它涵盖了多个研究领域的技术，如分布式系统、机器学习和隐私。这里我们给出了fls的正式定义。

我们假设有N个不同的值，每个值记为Ti，其中i∈\[1，N\]。我们用Di表示Ti的数据。对于非联邦场景，每一方Ti仅使用其本地数据Di来训练机器学习模型Mi， Mi的预测精度表示为Pi。在联邦场景中，各方共同训练一个模型$$M_f$$，各方Ti根据其具体的隐私限制保护其数据Di。$$M_f$$的预测精度记为$$P_f$$。那么，对于一个有效的FLS，存在i∈\[1，N\]使得$$P_f>P_i$$。

注意，在上面的定义中，我们只要求存在任何一方可以从FL实现更高的模型质量。尽管一方可能不会从FL得到一个更好的模型，他们仍可能加入联盟，使与其他各方达成协议要求的其他激励机制\(例如，金钱\)。

### 2.3 与传统联邦系统比较

联邦的概念可以在现实世界中找到，比如商业和体育。联邦的主要特点是合作。联邦不仅在社会中普遍出现，而且在计算中也起着重要的作用。在计算机科学中，联邦计算系统在不同的背景下一直是一个有吸引力的研究领域。

1990年前后，有很多关于联邦数据库系统\(fdbs\)的研究\[185\]。FDBS是一组相互合作的自治数据库。正如之前的研究指出的\[185\]，FDBS的三个重要组成部分是自主性、异构性和分布性。

* 自主性。参与FDBS的数据库系统\(DBS\)是自治的，这意味着它处于独立和独立的控制之下。双方仍然可以在没有FDBS的情况下管理数据。
* 异构性。在FDBS中，数据库管理系统可以是不同的。例如，差异可能在于数据结构、查询语言、系统软件需求和通信能力。
* 分布性。在FDBS建立之前，由于存在多个dbs，不同dbs的数据分布可能不同。一个数据记录可以水平或垂直地划分到不同的dbs中，也可以在多个dbs中重复，以提高可靠性。

最近，随着云计算的发展，对联邦云计算进行了许多研究\[113\]。联合云\(FC\)是多个外部和内部云计算服务的部署和管理。通过将部分外包给成本效率更高的地区，云联合概念可以进一步使成本降低。资源迁移和资源冗余是联邦云的两个基本特征\[113\]。首先，资源可以从一个云提供商转移到另一个云提供商。迁移可以实现资源的重新定位。第二，冗余允许在不同的领域并发使用类似的服务特性。例如，可以根据相同的计算逻辑在不同的提供者处对数据进行分区和处理。总的来说，不同资源的调度是联邦云系统设计中的一个关键因素。

fls和传统联邦系统之间有一些相似之处和不同点。首先，联邦的概念仍然适用。其共同和基本的理念是多独立主体的合作。因此，考虑当事人之间的异构性和自治性的观点仍然可以应用于fls。其次，分布式系统设计中的一些因素对fls仍然很重要。例如，在各方之间共享数据的方式可能影响系统的效率。由于差异，这些联邦系统对协作和约束的重视程度不同。fdbs关注的是分布式数据的管理，FCs关注的是资源的调度，而fls关注的是多方的安全计算。FLSs带来了新的挑战，如分布式训练的算法设计和隐私限制下的数据保护。

图1显示了这三个研究领域每年的论文数量。我们在谷歌学术中搜索关键词“federated database”、“federated cloud”、“federated learning”进行论文统计。虽然联邦数据库在30年前就被提出了，但近年来仍有约400篇论文提到了联邦数据库。联邦云的流行度在一开始比联邦数据库增长得更快，但在最近几年似乎有所下降，这可能是因为云计算变得更加成熟，联邦的动机也在减弱。对于FL来说，相关论文的数量正在迅速增加，去年已经达到约4400篇。如今，“数据岛”现象是普遍且日益成为机器学习中的一个重要问题。此外，公众对隐私的关注和社会意识也在不断提高。因此，我们预计FL的受欢迎程度将持续增加，至少5年，直到可能有成熟的fls。

![image-20210507104204412](https://gitee.com/zlt_shadow/res/raw/master/images-bed/paper5/figure-1.png)

图1：关于“联邦数据库”、“联邦云”和“联邦学习”的相关论文数量

## 3 系统组件

FLS中有三个主要组件：当事人\(例如，客户端\)、管理者\(例如，服务器\)和用于训练机器学习模型的通信计算框架。

### 3.1 当事人

在fls中，各当事人是数据所有者，也是FL的受益者。他们可以是组织或移动设备，分别命名为跨竖井或跨设备场景\[100\]。我们考虑影响fls设计的各方的以下特性。

第一，各方的硬件能力如何？硬件容量包括计算能力和存储能力。如果参与者是手机，则容量较弱，参与者无法进行大量计算和训练大型模型。例如，Wang等人\[211\]考虑FL中的资源约束设置。他们设计了一个包括资源预算的目标，并提出了一种确定本地更新轮数的算法。

第二，各方的规模和稳定性如何？对于组织来说，与移动设备相比，规模相对较小。此外，跨竖井场景的稳定性优于跨设备场景。因此，在跨竖井场景中，我们可以期望每一方能够在整个联邦进程中不断地执行计算和通信任务，这是许多研究中常见的场景\[120，46，188\]。如果双方是移动设备，系统需要处理\[26\]连接丢失等可能的问题。此外，由于设备的数量可能非常大\(例如，数百万\)，在FL中假定所有设备都参与每一轮是不现实的。广泛使用的方法是在每一轮中选择一部分设备进行计算\[146，26\]。

最后，各方之间的数据分布是什么？通常，无论跨设备还是跨竖井场景，非iid\(相同且独立分布\)数据分布都被认为是联邦学习中一个实用且具有挑战性的设置\[100\]，在最近的工作中\[120，233，127，208\]的实验中得到了评估。这种非iid数据分布在各组织之间可能更为明显。例如，银行和保险公司可以使用FL来提高他们的预测5例如，一个人是否可以偿还贷款，这个人是否会购买保险产品\)，而这些组织的功能甚至可以有很大的差异。迁移学习\[165\]、元学习\[65\]和多任务学习\[175\]的技术可能有助于结合各种各样的参与者的知识。

### 3.2 管理器

在跨设备场景中，管理器通常是一个强大的中央服务器。负责对全局机器学习模型进行训练，管理各方与服务器之间的通信。服务器的稳定性和可靠性非常重要。一旦服务器不能提供准确的计算结果，FLS可能会产生一个坏的模型。为了解决这些潜在的问题，区块链\[195\]可能是一种提供分散解决方案的技术，以提高系统的可靠性。例如，Kim等人\[109\]利用区块链代替了系统中的中央服务器，区块链允许交换设备更新并为它们提供奖励。

在跨竖井场景中，由于组织被期望拥有强大的机器，管理器也可以是主导FL过程的组织之一。这在垂直FL\[225\]中特别使用，我们将在4.1节中详细介绍。在Liu等人的垂直FL场景中\[136\]，数据的特征在各方之间垂直划分，只有一方拥有标签。拥有标签的一方自然被认为是FL管理器。

一个挑战可能是很难找到一个受信任的服务器或一方作为管理者，特别是在跨竖井场景中。然后，一个完全去中心化的场景可以是一个很好的选择，在那里各方可以直接相互交流，几乎平等地为全球机器学习模型训练做出贡献。在这里，管理器实际上是所有的当事人。这些各方共同设置一个FL任务并部署FLS。Li等人\[120\]提出了一种联邦梯度推进决策树框架，每一方依次训练决策树，最终的模型是所有树的组合。设计一个具有合理通信开销的完全分散的FLS是一个挑战。

### 3.3 通信控制系统框架

在fls中，计算发生在各方和管理器上，而通信发生在各方和管理器之间。通常，计算的目的是为了模型训练，通信的目的是为了交换模型参数。

联邦平均\(Federated Averaging， FedAvg\)\[146\]是2016年提出的一个基本框架，应用广泛，如图2a所示。在每次迭代中，服务器首先将当前全局模型发送给选定的各方。然后，被选方用他们的本地数据更新全局模型。接下来，将更新后的模型发送回服务器。最后，服务器对所有接收到的本地模型进行平均，得到一个新的全局模型。FedAvg重复上述过程，直到达到指定的迭代次数。服务器的全局模型是最终的输出。

FedAvg是一个集中的FL框架，而Li等人提出的SimFL\[124\]则是一个分散的FL框架。在SimFL中，不需要受信任的服务器。在每次迭代中，各方首先更新其本地数据的梯度。然后，渐变被发送到选定的一方。接下来，被选方使用其本地数据和梯度来更新模型。最后，将模型发送给所有其他方。为了确保公平性，并利用来自不同方的数据，每个方都被选中进行相同轮数的模型更新。SimFL重复指定的迭代次数并输出最终的模型。

一个具有挑战性和重要的方向是研究计算和通信代价之间的权衡。具体来说，人们可能想知道收敛速度、两轮通信之间的本地计算迭代和总通信轮之间的关系。最近，Li等人\[127\]研究了FedAvg在非iid数据分布上的收敛性。他们的理论表明，收敛速度与总本地迭代次数\(即两个通信轮之间的本地计算迭代次数乘以总通信轮数\)成反比。

![image-20210507111753280](https://gitee.com/zlt_shadow/res/raw/master/images-bed/paper5/figure-2.png)

图2：联邦学习框架

另一个值得注意的方面是，除了模型参数之外，还可能需要更多的信息来进行计算和通信，以满足隐私保证。模型参数容易受到推理攻击，可能暴露训练数据的敏感信息\[186，155，66\]。一种可能的解决方案是安全多方计算\[131，76\]，它使各方能够在他们的输入上联合计算一个功能，同时保持这些输入的私密性。然而，加密的计算开销和发送密钥的通信开销是重要的，可能成为整个FL过程的瓶颈。因此，效率是fls中的一个重要指标，许多人一直致力于减少开销，特别是通信规模\[111，146，183，27，122\]。

## 4 分类

考虑到不同fls的通用系统抽象和构建模块，我们从数据划分、机器学习模型、隐私机制、通信架构、联邦规模和联邦动机6个方面对fls进行了分类。这些方面包括以前的FLSs\[185， 113\]中的公共因素\(如数据划分、通信体系结构\)和FLSs的独特考虑\(如机器学习模型和隐私机制\)。此外，这些方面可以用于指导fls的设计。图3显示了fls分类的摘要。

![image-20210507111936296](https://gitee.com/zlt_shadow/res/raw/master/images-bed/paper5/figure-3.png)

图3：联邦学习系统的分类

让我们用一个直观的例子来解释这六个方面。不同地区的医院想要进行FL以提高肺癌预测任务的效率。然后，设计这样一个FLS需要考虑六个方面。

* **数据分区。**我们应该研究病人档案在医院中的分布情况。虽然医院可能有不同的病人，但他们也可能对一个普通病人有不同的认识。因此，我们必须同时利用FL中的非重叠实例和特性。
* **机器学习模型。**我们应该弄清楚应该采用哪种机器学习模型来完成这样的任务。例如，如果我们想对诊断图像执行分类任务，我们可能想在FL中训练一个卷积神经网络。
* **隐私机制。** 我们必须决定使用什么技术来保护隐私。由于病人记录是非常私密的，我们可能必须确保它们不能被交换的梯度和模型推断出来。差分隐私是实现隐私保障的一种选择。
* **通信架构。**我们必须确定通信架构。如果有一个值得信任的服务器，那么它可以是FL中的管理者。否则我们必须采用去中心化设置。
* **联邦规模。**与移动设备上的FL不同，在这种情况下，我们的federation规模相对较小，且稳定性较好。另外，每一方都有相对较大的计算能力，这意味着我们可以在FL过程中容忍更多的计算操作。
* **联邦动机。**我们应该考虑各方鼓励他们参与FL的动机。对于医院来说，一个明确而直接的动机是提高肺癌预测的准确性。然后，FL应实现一个比各方本地训练具有更高精度的模型。

### 4.1 数据分区

根据数据在样本和特征空间上的分布方式，通常可以将fls分为水平fls、垂直fls和混合fls\[225\]。

在水平FL中，不同方的数据集具有相同的特征空间，但在样本空间上很少有交集。这是一种自然的数据分区，特别是在跨设备场景时，不同的用户试图使用FL在同一任务上改进他们的模型性能。而且，大多数FL研究采用水平分区。由于本地数据具有相同的特征空间，因此双方可以使用具有相同模型体系结构的本地数据来训练本地模型。通过对所有本地模型求平均值，可以简单地更新全局模型。水平联邦学习的一个基本和流行的框架是FedAvg，如图2所示。唤醒词识别\[114\]，如“Hey Siri”和“OK Google”，是水平份去的典型应用，因为每个用户用不同的声音说同一个句子。

在垂直FL中，不同方的数据集具有相同或相似的样本空间，但特征空间不同。对于垂直FLS，通常采用实体对齐技术\[224，49\]来收集各方的重叠样本。然后利用重叠数据用加密方法训练机器学习模型。Cheng等人提出了一种无损的垂直FLS，使各方能够协作训练梯度推进决策树。他们使用隐私保护实体对齐来寻找双方之间的共同用户，并用其梯度来联合训练决策树。政府机构之间的合作可以看作是一种垂直划分的情况。假设税务部门需要居民住房数据来制定税收政策，居民住房数据存储在住房部门。同时，住房部门也需要居民的税务信息，这些信息由税务部门保存，以调整居民的住房政策。这两个部门共享相同的样本空间\(即国家的所有居民\)，但每个部门只有一部分特征\(如住房或税务相关的个人数据\)。

在其他许多应用中，现有的fls大多集中在一种分区上，而各方之间的数据分区可能是水平分区和垂直分区的混合。我们以癌症诊断系统为例。一些医院希望建立一个用于癌症诊断的FLS，但每家医院都有不同的患者以及不同种类的医疗检查结果。迁移学习\[165\]是一种可能的解决方案。Liu等人\[136\]提出了一种安全的联邦迁移学习系统，该系统可以使用公共实例学习各方特征之间的表示。

### 4.2 机器学习模型

由于FL是用来解决机器学习问题的，所以双方通常希望在特定的任务上训练一个最先进的机器学习模型。在开发新模型或重新设计现有模型以适应联邦环境方面已经做出了许多努力。在这里，我们考虑目前广泛使用的模型。目前最流行的机器学习模型是神经网络\(NN\)，它在图像分类和单词预测等许多任务中取得了最先进的结果\[112，194\]。基于随机梯度下降的联邦学习研究有很多\[146，208，26\]，可以用来训练神经网络。

另一种广泛使用的模型是决策树，与网络相比，决策树训练效率高，易于解释。基于树的FLS是专为单个或多个决策树\(如梯度推进决策树\(gbdt\)和随机森林\)的训练而设计的。gbdt是近年来特别流行的一种方法，它在许多分类和回归任务中都有很好的性能。Li等人\[120\]和Cheng等人\[46\]分别对水平和垂直分割数据上的gbdt提出了fls。

除了神经网络和树，线性模型\(如线性回归，逻辑回归，支持向量机\)是经典和易于使用的模型。有一些非常完善的线性回归和逻辑回归系统\[159，87\]。与其他复杂模型\(如nn\)相比，这些线性模型基本上容易学习。

目前，许多基于随机梯度下降的FL框架\[146，110，211，123\]被提出，这是神经网络和逻辑回归等许多模型的典型优化算法。然而，为了提高FL的有效性，我们可能需要利用模型架构\[208\]。由于FLS的研究还处于初级阶段，FLS在更好地支持先进的模型方面还存在差距。

### 4.3 隐私机制

虽然在FL中没有公开本地数据，但交换的模型参数仍然可能泄漏有关数据的敏感信息。针对机器学习模型的攻击有很多\[205，66，31，186，155，148\]，如模型反转攻击\[66\]和隶属度推理攻击\[186\]，这些攻击可以通过访问模型来潜在地推断原始数据。此外，目前有许多隐私保护机制，如差分隐私\[58\]和k-匿名\[60\]，它们提供了不同的隐私保障。调查总结了现有隐私机制的特点\[204\]。在这里，我们介绍了当前FLSs中用于数据保护的两种主要方法：加密方法和差分隐私。

密码方法如同态加密\[16，87，28，34，82，173，174，232，234，133\]和安全多方计算\(SMC\)\[184， 38， 24， 55， 25， 118， 18， 68， 107， 206， 41， 72\]被广泛应用于隐私保护机器学习算法中。基本上，双方在发送消息之前必须对消息进行加密，对加密的消息进行操作，并对加密的输出进行解密，以获得最终结果。应用上述方法，通常可以很好地保护fls的用户隐私\[103，229，105，162，230\]。例如，SMC\[76\]保证所有各方除了输出之外不能学到任何东西。然而，SMC容易受到推理攻击。此外，由于额外的加密和解密操作，这样的系统承受着极高的计算开销。

差分隐私\[58，59\]保证了单个记录不会对函数的输出产生太大影响。许多研究采用差分隐私\[37，19，9，220，237，93，121，199，239，128\]来保护数据隐私，以确保当事人不知道个人记录是否参与了学习。通过向数据或模型参数中注入随机噪声\[9，121，189\]，差分隐私为单个记录提供了统计隐私保证，并防止对模型的推理攻击。由于学习过程中的噪声，这类系统往往产生不太准确的模型。

需要注意的是，上述方法是相互独立的，一个FLS可以采用多种方法来增强隐私保障\[77，223\]。还有其他保护用户隐私的方法。一种有趣的基于硬件的方法是使用可信执行环境\(TEE\)，如Intel SGX处理器\[177，163\]，它可以保证加载在其中的代码和数据受到保护。这种环境可以在中央服务器内部使用，以增加其可信度。

虽然大多数现有的fls采用加密技术或差异隐私来实现良好的隐私保证，但这些方法的局限性似乎难以克服。在尽量减少这些方法带来的副作用的同时，寻找保护数据隐私和灵活的隐私要求的新方法也可能是一个很好的选择。例如Liu等\[136\]采用了一个较弱的安全模型\[56\]，使得系统更加实用。

与隐私级别相关，威胁模型在fls中也有所不同\[142\]。攻击可以来自FL过程的任何阶段，包括输入、学习过程和学习模型。

* **输入** 恶意方可以对FL进行数据投毒攻击\[42，115，13\]。例如，恶意方可以修改特定类训练样本的标签，使学习到的模型在该类上表现较差。
* **学习过程** 在学习过程中，各方可以进行模型投毒攻击\[17，221\]，上传设计好的模型参数。与数据投毒攻击一样，由于本地更新中毒，全局模型的精度可能非常低。除了模型投毒攻击外，拜占庭错误\[33，23，45，192\]也是分布式学习中常见的问题，在这种情况下，各方可能会任意行为，并上传随机更新。
* **学习模式** 如果被学习的模型被发表，就可以对其进行推理攻击\[66，186，148，155\]。服务器可以从交换的模型参数中推断出有关训练数据的敏感信息。例如，隶属关系推理攻击\[186，155\]可以推断一个特定的数据记录是否被用于训练。注意，推理攻击也可能由FL管理器在学习过程中进行，他可以访问各方的本地更新。

### 4.4 通信架构

在fls中有两种主要的通信方式：集中设计和分散设计。在集中式设计中，数据流通常是非对称的，这意味着管理者从各方聚集信息\(例如，本地模型\)并发回训练结果\[26\]。全局模型上的参数更新总是在这个管理器中完成。管理器和本地方之间的通信可以是同步的\[146\]或异步的\[222，190\]。在分散式设计中，通信在各方之间进行\[237，120\]，每一方都能够直接更新全局参数。

Google键盘\[86\]是集中式架构的一个例子。服务器从用户的设备收集本地模型更新，并训练一个全局模型，然后将其发送回用户进行推断，如图2a所示。可扩展性和稳定性是集中式FL系统设计的两个重要因素。集中式设计在现有研究中得到了广泛的应用，但在某些方面，集中式设计更受青睐，因为信息集中在一台服务器上可能会带来潜在的风险或不公平。最近，区块链\[242\]是一个流行的去中心化平台。为FL设计一个去中心化的系统仍然是一个挑战，在学习过程中，各方在通信方面几乎是平等的，不需要信任的服务器。医院间分散式癌症诊断系统就是分散式架构的一个例子。每家医院共享使用患者数据训练的模型，并获得用于\[29\]诊断的全局模型。在去中心化设计中，主要的挑战是很难设计一个协议，以合理的通信开销几乎公平地对待每个成员。由于没有中央服务器，训练是在各方进行的，所以一方可能需要向所有其他方收集信息，各方的沟通开销自然与各方的数量成正比。

### 4.5 联邦规模

根据联邦的规模，可以将fls分为两种典型类型：跨竖井fls和跨设备fls\[100\]。它们之间的区别在于参与者的数量和每个参与者中存储的数据量。

在跨竖井FLS中，当事人是组织或数据中心。通常有相对较少的参与者，每个参与者都有相对较大的数据量和计算能力。例如，亚马逊希望通过训练从世界各地数百个数据中心收集的购物数据来为用户推荐商品。每个数据中心都拥有庞大的数据量和充足的计算资源。此类FLS面临的一个挑战是如何在隐私模型约束下有效地将计算分发到数据中心\[243\]。

而在跨设备的FLS中，参与者的数量相对较大，每个参与者的数据量和计算能力都相对较小\[210\]。当事人通常是移动设备。谷歌Keyboard\[226\]是跨设备fls的一个例子。谷歌Keyboard的查询建议可以在FL的帮助下改进。考虑到能耗问题，不能要求设备进行复杂的训练任务。在这种情况下，系统应该足够强大，能够管理大量的参与者，并处理可能出现的问题，如设备和服务器之间的连接不稳定。

### 4.6 联邦动机

在实际的FL应用中，个体需要动机来参与到FLS中。动机可以是规章制度或激励措施。公司或组织内部的FL通常是由规章制度驱动的\(例如，公司不同部门的FL\)。但在许多情况下，法规不能强迫当事人提供数据。以谷歌Keyboard\[226\]为例，谷歌不能阻止不提供数据的用户使用谷歌Keyboard。但是那些同意上传输入数据的人可能会获得更高的单词预测精度。这种激励可以鼓励每个用户提供他们的数据，以提高整体模型的性能。然而，如何设计这样一个合理的协议仍然是一个挑战。

激励机制的设计对于FLS的成功至关重要。在区块链\[247，61\]中有一些成功的激励设计案例。系统内的各方可以是合作者，也可以是竞争者。其他的激励设计如\[102，101\]被提出以高质量的数据为FL吸引参与者。我们期望在fls下，博弈论模型\[182，99，154\]及其均衡设计应该被重新审视。即使在谷歌键盘的情况下，用户也需要被激励来参与这个协作学习过程。

## 5 现有研究综述

在这节中，我们根据第4节所考虑的方面对现有的FLSs进行了总结和比较。

### 5.1 方法

我们在谷歌Scholar中搜索关键词“联邦学习”，以了解现有的关于外语学习的研究。这里我们只考虑在计算机科学界发表的研究。

由于联邦的规模和联邦的动机是一个问题，所以我们没有从这两个方面对现有的研究进行比较。为了表示方便，我们分别用“NN”、“DT”和“LM”来表示神经网络、决策树和线性模型。此外，我们使用“CM”和“DP”分别表示密码方法和差分隐私。需要注意的是，一些研究中的算法\(如联邦随机梯度下降\)可以用于学习许多机器学习模型\(如logistic回归和神经网络\)。因此，在“模型实现”一栏中，我们给出了相应论文实验中实现的模型。此外，在“主要领域”一栏中，我们指出了论文研究的主要领域。

### 5.2 个人研究

我们总结了现有的流行和最先进的研究工作，如表1所示。从表1中，我们有以下四个关键发现。

![image-20210508092739847](https://gitee.com/zlt_shadow/res/raw/master/images-bed/paper5/table-1.png)

表1：已发表研究的比较。LM为线性模型。DM为决策树。NN表示神经网络。CM表示密码方法。DP表示差分隐私。

首先，大多数现有的研究都考虑水平数据分区。我们推测，部分原因是水平数据分区的实验研究和基准测试相对于垂直数据分区来说是比较成熟的。然而，垂直的FL在现实世界中也很常见，特别是在不同的组织之间。垂直的FL可以使更多的各方之间的合作。因此，要想填补这一空白，必须加大对垂直FL的投入。

其次，大多数研究考虑在没有任何隐私保证的情况下交换原始模型参数。如果未来发现对机器学习模型的更强大的攻击，这可能就不正确了。目前，提供隐私保障的主流方法是差分隐私和加密方法\(如安全多方计算和同态加密\)。不同的隐私可能会影响最终的模型质量很多。此外，加密方法带来了大量的计算和通信开销，可能成为fls的瓶颈。我们期待以一种廉价的方式，以合理的隐私保证来满足规定。

第三，集中式设计是当前实现的主流。在它们的场景中需要一个可信的服务器。但是，可能很难找到受信任的服务器，特别是在跨竖井场景中。去除中央服务器的一种简单方法是，各方将模型参数共享给所有其他各方，并且每一方还在本地维护相同的全局模型。与集中式场景相比，该方法带来了更多的通信和计算开销。对于分散式结构下的实际FL，还需要进行更多的研究。

最后，提高FL的有效性、效率和私密性是FL的主要研究方向\(也是挑战\)，这也是评价FL的三个重要指标。与此同时，还有许多其他的研究课题，如公平和激励机制。由于FL涉及到很多研究领域，我们相信FL将会吸引更多的研究者，在不久的将来我们会看到更多有趣的研究。

#### 5.2.1 有效性改进

虽然有些算法是基于SGD的，但其他算法是专门为一种或几种模型架构设计的。因此，我们将它们分类为基于SGD的算法，并相应地对专用算法进行建模。

**基于SGD**

如果我们将一方的本地数据视为单个批次，那么可以通过每轮执行单个批次梯度计算\(即FedSGD\[146\]\)在联邦场景中轻松实现SGD。然而，这种方法可能需要大量的通信回合才能收敛。为了减少通信轮数，3.3节和图2a中介绍的FedAvg \[146\]现在是一个基于SGD的典型而实用的FL框架。在FedAvg，每一方与SGD就其本地模型进行多轮训练。然后，全局模型的权重被更新为本地模型的权重的平均值。全局模型被发送回各方以完成全局迭代。通过平均权重，本地各方可以在其本地模型上采取多个梯度下降步骤，从而可以与FedSGD相比减少通信轮的数量。

Koneˇcn‘y等人。\[110\]提出联邦SVRG\(FSVRG\)。联邦SVRG和联邦平均的主要区别在于本地模型和全局模型参数的更新方式\(即步骤2和步骤4\)。在联邦SVRG中，模型权重的更新公式是基于随机方差减少梯度\(SVRG\)\[97\]和分布式近似牛顿算法\(DANE\)的。他们将他们的算法与CoCoA+\[143\]和简单分布式梯度下降等其他基线进行了比较。对于逻辑回归模型，他们的方法可以在相同的通信轮次下获得更好的准确性。联合平均和联合SVRG之间没有可比性。

联邦学习中的一个关键挑战是本地数据分布的异构性，这会大大降低联邦学习的性能\[123，104，127\]。为了应对这一挑战，李等人\[123\]推荐FedProx。由于过多的本地更新可能导致平均模型远离全局最优，FedProx在本地目标中引入了额外的最近项来限制本地变化量。Scaffold\[104\]不直接限制本地更新的大小，而是应用方差减小技术来校正本地更新。FedProx和Scaffold改善了FedAvg的本地训练阶段，FedNova\[209\]改善了FedAvg的聚集阶段。该算法考虑了各方本地更新的异构性，在平均前根据本地更新对本地模型进行归一化处理。

上述研究的目标是在非iid数据场景下，尽量减少对整个训练数据集的损失。另一种解决方案是设计个性化的联邦学习算法，其目标是让每一方学习一个个性化的模型，该模型可以很好地处理其本地数据。Per-FedAvg\[62\]在FedAvg中应用了与模型无关的元学习\[65\]框架的思想。pFedMe\[54\]使用Moreau信封帮助分解个性化模型优化。Hanzely等\[84\]建立了个性化联邦学习优化的通信复杂度和本地oracle复杂度的下界。采用加速近端梯度下降法\(accelerated proximal gradient descent， APGD\)和加速L2SGD+\[83\]，可获得最优复杂度界限。IFCA\[74\]假设各方按当地目标划分为群组。其思想是在估计集群特性的同时，尽可能减少损失函数。

与非iid数据场景相关的另一个研究方向是针对本地分布的可能组合设计健壮的联邦学习。Mohri等。\[152\]提出一个新的框架命名的不可知论FL。而不是减少损失对平均分布在数据分布从本地客户，他们试图训练集中模型优化的任何可能的目标分布由客户端分布的混合物。FedRobust\[172\]认为是一种结构化仿射分布转移。提出梯度下降爬升法来解决分布式极大极小优化问题。

虽然上述研究考虑了数据的异构性，但在联邦学习中也可能存在本地模型的异构性。各方可以用不同的体系结构训练模型。FedDF\[130\]利用知识蒸馏\[90\]来聚合本地模型。它假设服务器端有一个公共数据集，可以用来提取本地模型的知识并更新全局模型。

基于sgd的垂直联合学习的研究较少。\[137\]提出了用于垂直FL的联邦随机块坐标下降\(federal Stochastic Block Coordinate Descent， FedBCD\)。通过应用坐标下降，各方在传递中间结果之前，更新自己的本地参数多次。它们还为FedBCD提供了收敛性分析。Hu等人\[92\]提出了垂直FL的FDML，假设各方都有标签。它不是交换中间结果，而是聚合来自每个参与方的本地预测。

**神经网络**

虽然神经网络可以使用SGD优化器进行训练，但如果模型架构也可以被利用，我们可以潜在地增加模型的实用性。Y urochkin等人\[233\]应用贝叶斯非参数机制为多层感知器开发了概率联邦神经匹配\(PFNM\)\[70\]。他们使用beta -伯努利过程信息匹配过程将局部模型组合成联邦全局模型。实验表明，该方法在IID和非IID数据分区上均优于FedAvg。

Wang等人\[208\]展示了如何将PFNM应用于卷积神经网络\(CNNs\)和lstm \(long -term memory networks\)。此外，他们还利用该模型体系结构，提出了一种分层匹配的联邦匹配平均方案。具体来说，它们每次使用匹配平均来更新全局模型的一层，这也减少了通信规模。实验表明，FedMA在cnn和lstm上的性能优于FedAvg和FedProx\[123\]。

另一项关于神经网络垂直联合学习的研究是分裂学习\[202\]。V epakomma等人\[202\]提出了一种名为SplitNN的新范式，其中神经网络分为两部分。每个参与方只需要对网络的几层进行训练，然后将切割层的输出传输给有标签的一方，完成剩下的训练。

**树**

除了神经网络，决策树在学术界和工业界也得到了广泛的应用\[40，106，64，121\]。与神经网络相比，树的训练和推理是高效的。但是，树的参数不能通过SGD直接优化，这意味着基于SGD的FL框架不适用于树的学习。我们需要专门的树框架。在树模型中，梯度增强决策树\(GBDT\)模型\[40\]是比较流行的。联邦GBDT有很多研究。

目前已有一些关于水平联邦gbdt的研究。Zhao等人\[237\]提出了gbdt的第一个FLS。在他们的框架中，每个决策树都是本地训练的，而不需要各方之间的通信。一组训练好的树被送到下一组去连续训练一些树。差分隐私用于保护决策树。Li等人\[120\]利用位置敏感哈希\[53\]在联邦gbdt的构建中利用相似信息。它们通过聚合相似实例的梯度来利用局部团体的数据分布。与安全多方计算相比，在一个较弱的隐私模型中，他们的方法是有效和高效的。

Liu等人\[139\]提出了一种用于移动人群感知的联邦极限推进学习框架。他们采用秘密共享来实现gbdt的隐私保护学习。Liu等人\[138\]提出了联邦森林，该方法可以在垂直的FL场景上训练随机森林。在构建每个节点时，具有相应拆分特性的一方负责对样本进行拆分并共享结果。他们加密通信数据以保护隐私。他们的方法和非联邦版本一样准确。

Cheng等人提出了SecureBoost，一个用于gbdt垂直FL场景的框架。在他们的假设中，只有一方拥有标签信息。他们使用实体对齐技术获取公共数据，然后建立决策树。采用加同态加密来保护梯度。

**线性/逻辑回归**

线性/逻辑回归可以通过SGD实现。这里我们展示的研究不是基于sgd，专门为线性/逻辑回归设计的。

在水平FL场景中，Nikolaenko等人\[159\]提出了一种隐私保护岭回归系统。他们的方法结合了同态加密和Y的乱码电路来实现隐私要求。需要一个额外的求值器来运行算法。Chen等人提出了一种隐私保护岭回归系统。他们的方法结合了安全求和和同态加密来实现隐私要求。他们提供了他们的方法和以前最先进的方法之间的通信和计算开销的完整比较。

在垂直FL场景中，Sanil等\[180\]提出了一种安全的回归模型。他们着重于线性回归模型和秘密共享的应用，以确保隐私在他们的解决方案。Hardy等人\[87\]提出了两方垂直联邦logistic回归的解决方案。它们采用实体解析和加法同态加密。

**其他**

有许多研究将FL与其他机器学习技术相结合，如多任务学习\[175\]、元学习\[65\]、强化学习\[150\]和迁移学习\[165\]。

Smith等人\[188\]将FL与多任务学习结合起来\[32，235\]。他们的方法考虑了联邦环境下MTL的高通信成本、掉队和容错问题。Corinzia和Buhmann\[51\]提出了一种基于非凸模型的联邦MTL方法。他们将中心服务器和局部参与者视为贝叶斯网络，并使用变分方法进行推理

Chen等人\[39\]在FedAvg的学习过程中采用元学习。双方在局部训练中并交换MAML的梯度，而不是训练局部神经网络和交换模型参数，而是采用model - agnostic元学习\(MAML\)\[65\]算法。Jiang等人\[96\]根据现有的MAML算法来解释FedAvg。此外，他们使用爬虫算法\[157\]对FedAvg训练的全局模型进行微调。他们的实验表明，元学习算法可以提高全局模型的有效性。

Liu等人\[132\]提出了终身联合强化学习框架。采用迁移学习技术，训练一个全局模型来有效记住机器人在强化学习中学习到的内容。

Dai等\[52\]在联邦学习中考虑了贝叶斯优化。他们提出了联合汤普森抽样，以解决通信效率和客户端的异构性。他们的方法可能用于联邦学习中的参数搜索。

**总结**

我们将上述工作总结如下。

* 随着基于sgd的框架的广泛研究和应用，近来更多的研究集中在模型专门化的FL上。我们希望通过模型专门化的方法获得更好的模型精度。同时，我们鼓励研究人员研究联邦决策树模型。与神经网络相比，树型模型具有较小的模型规模和易于训练的特点，在FL中可以降低通信和计算开销。
* 对FLD的研究仍处于早期阶段。很少有研究将FL用于训练最先进的神经网络，如ResNeXt\[144\]和EfficientNet\[197\]。如何设计有效实用的算法来训练复杂的机器学习模型仍然是一个具有挑战性的研究方向。
* 虽然大多数研究集中在水平FL上，但对于垂直FL还没有完善的算法。然而，在涉及多个组织的实际应用中，垂直联邦场景是常见的。我们期待在这一有前景的领域进行更多的研究。

#### 5.2.2 沟通效率

使用高性能计算社区的现代硬件和技术\[140，117，119\]可以加速FL中的计算\[216，217\]，而FL的研究主要是为了减少FL过程中的通信规模。

Koneˇcn ' y等\[111\]提出了结构化更新和草图更新两种方式来降低联邦平均中的通信成本。第一种方法限制本地更新的结构，并将其转换为两个更小的矩阵的乘法。在学习过程中只发送一个小矩阵。第二种方法使用有损压缩方法来压缩更新。他们的方法可以降低两个数量级的通信成本，收敛速度略有下降。Zhu和Jin\[245\]设计了一种同时最小化通信代价和全局模型测试误差的多目标进化算法。将通信代价最小化和全局学习精度最大化作为两个目标，他们将FL定义为一个双目标优化问题，并用多目标进化算法求解。Jeong等人\[94\]提出了一个用于具有非iid本地数据的设备的FL框架。他们设计了联合蒸馏，其通信大小取决于输出维数，而不是模型大小。此外，他们还提出了一种使用生成对抗网络\(GAN\)使训练数据集成为IID的数据增强方案。许多其他研究也为非iid数据设计了专门的方法\[240，127，135，228\]。Sattler等人\[183\]提出了一种新的压缩框架，称为稀疏三元压缩\(STC\)。STC采用了稀疏化、三元化、错误积累和最优Golomb编码对通信进行压缩。他们的方法对非iid数据和大量的当事人是鲁棒的。

除了通信规模之外，还可以改进通信体系结构，提高训练效率。Marfoq等人\[145\]考虑了跨竖井联邦学习的拓扑设计。他们提出了一种找到吞吐量最优拓扑的方法，可以显著减少训练时间。

#### 5.2.3 隐私性、鲁棒性和攻击

虽然在FL中没有交换原始数据，但模型参数也会泄漏训练数据的敏感信息\[186，155，214\]。因此，为交换的本地更新提供隐私保证是很重要的。

差分隐私是一种流行的提供隐私保障的方法。Geyer等人\[71\]从客户端级别的角度在联邦平均中应用差异隐私。他们使用高斯机制来扭曲梯度更新的总和，以保护整个客户的数据集，而不是单个数据点。McMahan等\[147\]在LSTM训练中采用了联邦平均法。它们还使用客户端级别的差分隐私来保护参数。Bhowmick等人\[22\]采用本地差分隐私保护FL中的参数。为了提高模型质量，他们考虑了一个实际的威胁模型，希望解码个人数据，但对他们的先验信息很少。在这个假设下，他们可以更好地利用隐私预算。

Bonawitz等\[25\]在联邦平均的基础上采用安全多方计算保护局部参数。具体来说，他们提出了一种安全聚合协议来安全计算基于秘密共享的向量和\[184\]。他们还讨论了如何将差异隐私与安全聚合相结合。

Truex等人\[200\]结合了安全多方计算和差分隐私来保护FL的隐私。他们使用差分隐私将噪声注入到本地更新中。在发送到中央服务器之前，噪声更新将使用Paillier加密系统\[164\]进行加密。

在针对FL的攻击中，常见的一种攻击是后门攻击，它的目的是通过交换设计好的本地更新来获得糟糕的全局学习模型。

Bagdasaryan等人\[17\]对FL进行模型投毒攻击，恶意方将攻击模型提交给服务器，使得全局模型可能对投毒数据过拟合。安全多方计算以保护模型参数的机密性为目的，无法防止此类攻击。Bhagoji等人\[21\]也研究了对FL的模型投毒攻击，由于平均步骤会减少恶意模型的影响，所以采用了显式的推进方式来增加提交权值更新。Sun等人\[193\]在联邦EMNIST数据集上进行实验，评估联邦学习的后门攻击和防御，看看哪些因素会影响对手的性能。他们发现，在缺乏防御的情况下，攻击的性能在很大程度上取决于出现的对手的比例和目标任务的“复杂性”。我们拥有的“后门”任务越多，就越难在保持其在主要任务上的表现的同时，利用固定容量的模式“后门”。Wang等人\[207\]从理论的角度讨论了后门攻击，证明了在FL中是可行的。他们还提出了一种新的后门攻击类型，称为边缘情况后门，可以抵抗当前的防御方法。Xie等人\[221\]提出了一种针对FL的分布式后门攻击，他们将全局触发模式分解为局部模式。每个对抗方只使用一种本地模式。实验表明，该算法的分布式后门攻击性能优于中央后门攻击。

另一种攻击是拜占庭式攻击，在这种攻击中，对手完全控制一些经过身份验证的设备，并任意破坏网络。分布式学习中已有一些鲁棒聚合规则，如Krum\[23\]和Bulyan\[149\]。这些规则可以直接应用到联邦学习中。然而，由于每一方在联邦学习中执行多个本地更新步骤，所以研究联邦学习中的拜占庭攻击和防御是很有趣的。Li等人\[116\]提出了RSA，一种用于非iid数据设置的联邦学习的拜占庭鲁棒随机聚集方法。Fang等人\[63\]提出了拜占庭鲁棒联邦学习方法的模型投毒攻击。他们的方法的目标是修改本地模型，使全局模型最大程度地偏离正确的更新方向的相反方向。

关于FL攻击的另一个研究方向是推理攻击。已有研究针对集中设置下训练的机器学习模型进行推理攻击\[66，186，155\]。对于联邦场景，Geiping等\[69\]表明可以从交换梯度的知识重建训练图像。

#### 5.2.4 公平与激励机制

Li等\[125\]在FedAvg的基础上考虑公平性，提出q-FedAvg。具体地说，他们根据当事人对模型的表现的差异来定义公平。如果方差越小，则模型越公平。因此，他们设计了一个受α-公平\[14\]启发的新目标。基于联邦平均，他们提出q-FedAvg来解决他们的新目标。q-FedAvg与FedAvg的主要区别在于更新模型参数的公式。

Kim等人\[109\]将区块链架构与FL结合起来，在联邦平均的基础上，使用区块链网络交换设备的本地模型更新，比中央服务器更稳定，可以为设备提供奖励。Kang等人\[101\]利用多权重主观逻辑模型设计了一个基于声誉的可靠FL工作者选择方案。他们还利用区块链以一种去中心化的方式为具有不可抵赖性和抗篡改属性的工人实现安全信誉管理。

**总结**

根据以上回顾，我们将5.2.2至5.2.4节的研究总结如下。

* 除了有效性，效率和私密性是FLS的另外两个重要因素。与这三个领域相比，对公平和激励机制的研究较少。我们期待更多的公平和激励机制的研究，以鼓励在现实世界中使用FL。
* 为了提高fls的效率，通信开销仍然是主要的挑战。大多数研究\[111，94，183\]试图减少每次迭代的通信规模。如何合理设置沟通轮数也是很有前景的\[245\]。计算和通信之间的取舍仍需进一步研究。
* 对于隐私保证，差分隐私和安全多方计算是两种流行的技术。然而，差分隐私可能会显著影响模型的质量和安全多方计算可能非常耗时。设计一个实用的具有强大隐私保证的FLS仍然是一项挑战。此外，针对中毒攻击的有效鲁棒算法尚未得到广泛应用。

#### 5.2.5 应用

与FL相关的一个领域是边缘计算\[158，231，169，57，238\]，当事人是边缘设备。许多研究试图将FL与移动边缘系统相结合。FL在推荐系统\[15，35，244\]、自然语言处理\[86\]和交易欺诈检测\[241\]中也显示出了良好的效果。

**边缘计算**

Nishio和Yonetani\[161\]在实际移动边缘计算\(MEC\)框架中实现了联邦平均。它们使用MEC框架的操作符来管理异构客户机的资源。Wang等\[212\]在移动边缘计算系统中同时采用了分布式深度强化学习\(DRL\)和联邦学习。DRL和FL的使用可以有效地优化移动边缘计算、缓存和通信。Wang等人\[211\]在资源受限的MEC系统上执行FL。它们解决了如何有效利用边缘有限的计算和通信资源的问题。使用联邦平均，他们实现了许多机器学习算法，包括线性回归，支持向量机和CNN。他等人\[88\]也考虑了边缘设备中有限的计算资源。他们提出了FedGKT，每个设备只训练整个ResNet的一小部分，以减少计算开销。

**推荐系统**

Ammad-ud din等\[15\]提出了第一种联邦协同过滤方法。基于随机梯度方法，通过聚合局部更新，在全局服务器上训练项目因子矩阵。实验结果表明，与集中式方法相比，联邦方法几乎没有精度损失。Chai等\[35\]设计了一个联邦矩阵分解框架。他们使用联邦SGD来学习矩阵。此外，它们采用同态加密来保护通信梯度。Tan等\[196\]构建了一个基于FATE的联邦推荐系统\(federrecsys\)。FedRecSys已经用SMC协议实现了流行的推荐算法。算法包括矩阵分解、奇异值分解、分解机器和深度学习。

**自然语言处理**

Hard等\[86\]将FL应用于移动键盘下一词预测。它们采用联邦平均法学习LSTM的一种变体，称为耦合输入和忘记门\(CIFG\)\[78\]。与基于日志数据的服务器训练相比，FL方法能获得更好的查全率。

**交易欺诈检测**

Zheng等人\[241\]将FL引入信用卡交易欺诈检测领域。他们设计了一种新的基于元学习的联邦学习框架，称为深度K-tuplet网络，该网络不仅保证了数据的隐私性，而且与现有的方法相比，性能显著提高。

**总结**

根据以上研究，我们总结如下。

* 边缘计算自然适合跨设备联邦场景。如何有效地利用和管理边缘资源是将FL应用于边缘计算的一个重要问题。FL的使用可以给用户带来好处，特别是对改善移动设备业务。
* FL可以解决许多传统的机器学习任务，如图像分类和工作预测。由于规则和“数据岛”，联邦设置可能是未来几年的普遍设置。随着FL的快速发展，我们相信它将在计算机视觉、自然语言处理和医疗保健领域有更多的应用。

#### 5.2.6 基准

基准测试对于指导fls的开发非常重要。最近已经进行了多个与基准测试相关的工作，并且有几个基准测试框架可以在线使用。我们将其分为三类:1\)通用基准系统旨在全面评价语言支持系统，并对语言支持系统的各个方面进行详细描述；2\)目标基准针对集中在一个小领域的一个或多个方面，并试图优化该领域的系统性能；3\)数据集基准测试旨在为联邦学习提供专用数据集。

**通用基准系统**

FedML\[89\]是一个研究库，它提供了联邦学习和基准测试功能的框架。作为基准，它为多种ML模型和FL算法提供了全面的基线实现，包括FedAvg、FedNAS、V垂直FL和分离学习。此外，它支持三种计算模式，即分布式训练、移动设备上的训练和独立模拟。虽然它的一些实验结果目前仍处于初级阶段，但它是关于其功能的最全面的基准框架之一。

FedEval\[36\]是联邦学习的另一个计算模型。它以“ACTPR”模型为特征，即以准确性、通信、时间消耗、隐私性和鲁棒性为评价目标。它利用Docker容器提供一个独立的评估环境来解决硬件资源限制问题，并在实现中模拟了多达100个客户端。目前支持两种水平算法：FedSGD和FedAvg，并对MLP和LeNet等模型进行了测试。

OARF\[91\]为FL基准提供了一组实用程序和参考实现。它的特点是测量不同的组件在fls，包括FL算法，加密机制，隐私机制和通信方法。此外，它还具有数据集的现实分区，利用从不同来源收集的公共数据集来反映真实的数据分布。两种水平垂直算法都经过了测试。

Edge AIBench\[85\]为联邦学习应用提供了一个测试平台，并建模了4种应用场景作为参考实现：ICU患者监护仪、监控摄像头、智能家居和自动驾驶汽车。该实现是开源的，但目前没有实验结果的报告。

**目标基准**

Nilsson等人\[160\]提出了一种利用相关t检验来比较不同类型的联邦学习算法，同时绕过数据分布的影响的方法。FedAvg、FedSVRG\[111\]和CO-OP\[213\]三种FL算法在IID和非IID环境下的工作进行了比较，结果表明FedAvg在三种算法中，无论数据如何分区，其精度都是最高的。

Zhuang等人\[246\]利用基准分析来提高联邦人员再识别的性能。基准部分使用9个不同的数据集来模拟真实世界的情况，并使用联邦部分平均\(一种允许聚合部分不同模型的算法\)作为参考实现。

Zhang等人\[236\]提供一个基准针对semi-supervised联合学习场景，用户只有未标记的数据，和服务器只有少量的标签数据，并探索之间的关系最终模型的准确性和多个指标，包括数据的分布、算法和通讯设置，还有客户的数量。利用实验结果，他们的半监督学习改进方法获得了更好的泛化性能。

Liu等人\[134\]关注的是非iid问题，其中数据集在参与各方之间分布不均匀。他们的工作探索了定量描述数据分布偏态的方法，并提出了几种非iid数据集生成方法。

**数据集**

LEAF\[30\]是联邦学习最早的数据集提议之一。该算法包含6个不同领域的数据集，包括图像分类、情感分析和下一个字符预测。提供了一组实用程序，以IID或非IID的方式将数据集划分为不同的方。对于每个数据集，还提供了一个参考实现来演示该数据集在训练过程中的使用。

Luo等人\[141\]在他们的工作中展示了来自26个不同街道相机的真实世界图像数据集。该数据集中的图像包含7个不同类别的目标，适合于目标检测任务。提供联邦平均运行YOLOv3模型和Faster R-CNN模型的实现作为参考。

**总结**

总结以上研究，我们有以下发现。

* 基准测试在联邦学习的开发中起着重要的作用。通过不同类型的基准测试，我们可以定量地描述联邦学习的不同组件和方面。关于联邦学习中的安全和隐私问题的基准仍处于早期阶段，需要进一步发展。
* 目前还没有足够全面的基准系统来覆盖fls中的所有算法或应用类型。即使是最全面的基准测试系统也缺乏对系统每个级别的某些算法和评估指标的支持。进一步开发全面的基准系统需要广泛的FL框架的支持。
* 大多数基准研究使用的是从单个数据集分离出来的数据集，对于应该使用哪种类型的分离方法没有共识。类似地，关于非iid问题，对于非iid度的度量没有共识。使用FedML\[89\]和OARF\[91\]中提出的现实分区方法可以缓解这一问题，但对于大规模的联邦学习，现实分区并不适合，因为难以从不同来源收集数据。

### 5.3 开源系统

在本节，我们介绍五个开源FL系统：[Federated AI Technology Enabler \(FATE\)](https://github.com/FederatedAI/FATE)， [Google TensorFlow Federated \(TFF\)](https://github.com/tensorflow/federated)， [OpenMined PySyft](https://github.com/OpenMined/PySyft)， [Baidu PaddleFL](https://github.com/PaddlePaddle/PaddleFL)， 和 [FedML](https://github.com/FedML-AI/FedML).

#### 5.3.1 FATE

FATE是一个行业级的FL框架，旨在为不同组织之间提供FL服务。FATE的整体结构如图4所示。它有六个主要模块：EggRoll， FederatedML， FATE- Flow， FATE- Serving， FATE- Board和KubeFATE。EggRoll管理分布式计算和存储。它为其他模块提供计算和存储API。FederatedML包括联邦算法和安全协议。目前，它支持在水平和垂直联邦设置下训练多种机器学习模型，包括神经网络、GBDTs和逻辑回归。此外，它集成了安全多方计算和同态加密，以提供隐私保障。FATE-Flow是一个平台，用户可以定义他们的FL流程的管道。管道可以包括数据预处理、联邦训练、联邦评估、模型管理和模型发布。FA TE-Serving为用户提供推理服务。它支持加载FL模型并对其进行在线推理。FATE-Board是FATE的可视化工具。它提供了一种可视化的方式来跟踪作业执行和建模性能。最后，KubeFATE通过使用docker8或Kubernetes来帮助在集群上部署FATE。提供自定义部署和集群管理服务。一般来说，FATE是一个功能强大且易于使用的FLS。用户可以简单地设置参数来运行一个FL算法。此外，FA TE提供了关于其部署和使用的详细文档。然而，由于FATE提供了算法级接口，实践者必须修改FATE的源代码来实现他们自己的联邦算法。这对于非专业用户来说并不容易。

![image-20210508101206180](https://gitee.com/zlt_shadow/res/raw/master/images-bed/paper5/figure-4.png)

图4：FATE系统架构

#### 5.3.2 TFF

TFF为FL提供了基于TensorFlow的构建块。如图5所示，它提供了两个不同层的api：FL api和Federated Core \(FC\) API。FL API提供了高级接口。它包括三个关键部分，即模型、联邦计算构建器和数据集。FL API允许用户定义模型或简单地加载Keras\[80\]模型。联邦计算构造器包括典型的联邦平均算法。此外，FL API提供了模拟联邦数据集，以及访问和枚举FL本地数据集的函数。除了高级接口，FC API还包括作为FL过程基础的低级接口。开发人员可以在联邦核心中实现他们的函数和接口。具体来说，作为一个Python包，FC提供了Python接口，开发人员可以使用它们并编写新的Python函数。为了便于使用，特别是对于熟悉TensorFlow的开发人员，它支持许多类型，如Tenser类型、序列类型、元组类型和函数类型。最后，FC为FL提供了构建模块，它支持多个联邦操作符，如联邦求和、联邦reduce和联邦广播。开发人员可以定义自己的操作符来实现FL算法。总的来说，TFF是一个轻量级的系统，供开发人员设计和实现新的FL算法。目前，TFF只支持FedAvg，不提供隐私机制。它现在只能部署在一台机器上，联邦场景是通过模拟实现的。

![image-20210508101553622](https://gitee.com/zlt_shadow/res/raw/master/images-bed/paper5/figure-5.png)

图5：TFF系统架构

#### 5.3.3 PySyft

PySyft，首先由Ryffel等人提出\[176\]，是一个python库，为开发人员提供接口来实现他们的训练算法。虽然TFF是基于TensorFlow，但PySyft可以很好地与PyTorch和TensorFlow一起工作。虽然PySyft只支持FedAvg算法，但它提供了多种隐私机制，包括安全多方计算和差异隐私。此外，它可以部署在一台或多台机器上，不同客户机之间的通信是通过websocket API\[187\]进行的。然而，虽然PySyft提供了一组教程，但没有关于其接口和系统架构的详细文档。

#### 5.3.4 PaddleFL

PaddleFL是基于百度开发的深度学习平台PaddlePaddle的FLS。PaddleFL的系统结构如图6所示。在编译时，有四个组成部分，包括FL策略、用户定义的模型和算法、分布式训练配置和FL作业生成器。FL策略包括水平FL算法，如FedAvg。未来将集成垂直FL算法。除了提供FL策略外，用户还可以定义自己的模型和训练算法。分布式训练配置定义了分布式设置中的训练节点信息。FL作业生成器为联邦服务器和工作者生成作业。在运行时，有三个组件，包括FL服务器、FL工作者和FL调度器。server和worker分别是FL中的manager和parties。调度人员在每一轮中选择参加训练的工人。目前，PaddleFL的开发还处于初级阶段，文档和例子还不够清晰。

![image-20210508101706034](https://gitee.com/zlt_shadow/res/raw/master/images-bed/paper5/figure-6.png)

图6：PaddleFL系统架构

#### 5.3.5 FedML

FedML既提供了联邦学习框架，又提供了FL基准测试平台。作为一个FL框架，它的核心结构分为两层，如图7所示。在低层的FedMLcore中，实现了训练引擎和分布式通信基础设施。训练引擎处理与PyTorch的通信，并提供工作人员和协调人员来安排训练。通信基础设施利用多个后端\(如MPI和MQTT\)来处理分布式通信。高级FedML-API建立在它们之上，并提供训练模型、数据集和FL算法。参考应用程序/基准实现将进一步构建在FedML-API之上。FedML支持三种计算范式，即独立模拟、分布式计算和设备上训练，这为广泛的硬件需求提供了一个模拟环境。提供了所有支持的FL算法的参考实现。虽然部分实验结果与优化结果仍有差距，但为进一步研究提供了有用的信息。

![image-20210508101841132](https://gitee.com/zlt_shadow/res/raw/master/images-bed/paper5/figure-7.png)

图7：FedML系统架构

#### 5.3.6 其他

还有其他的闭源联邦学习系统。NVIDIA clara13启用了FL，采用集中式架构，通信信道加密。Clara FL的目标用户是医院和医疗机构\[126\]。平安科技旨在打造一个名为Hive的联邦学习系统，主要针对金融行业。Clara FL提供了api和文档，我们找不到Hive的官方文档。

#### 5.3.7 总结

总的来说，FATE， PaddleFL和FedML试图提供算法级api供用户直接使用，而TFF和PySyft试图提供更详细的构建块，以便开发人员可以轻松实现他们的FL过程。表2显示了两种开源系统之间的比较。在算法层面，FATE是最全面的系统，它支持许多水平和垂直场景下的机器学习模型。TFF和PySyft只实现FedAvg， FedAvg是FL中的一个基本框架，见5.2节。PaddleFL支持几种水平FL算法，目前在nn和逻辑回归。FedML集成了几种先进的FL算法，如FedOpt\[171\]和FedNova\[209\]。与FA TE、TFF和FedML相比，PySyft和PaddleFL提供了更多的隐私机制。PySyft涵盖了TFF所支持的所有特性，而TFF是基于TensorFlow的，PySyft与PyTorch合作更好。

![image-20210508102124371](https://gitee.com/zlt_shadow/res/raw/master/images-bed/paper5/table-2.png)

表2：现有的一些fls之间的比较。本表使用的表示法与表1相同。FedML没有发布版本。

## 6 系统设计

图8显示了在设计FLS时需要考虑的因素，包括有效性、效率、隐私和自主性。接下来，我们将对这些因素进行解释，并详细介绍设计原则。

![image-20210508102215611](https://gitee.com/zlt_shadow/res/raw/master/images-bed/paper5/figure-8.png)

图8：fls的设计因素

### 6.1 有效性

FLS的核心是\(多个\)有效算法\(算法\)。为了从表1所示的大量现有研究中确定要实现的算法，我们首先应该检查各方的数据划分。如果双方具有相同的特征但样本不同，可以使用FedAvg\[146\]用于神经网络，SimFL\[120\]用于树。如果当事方有相同的样本空间但不同的特性，可以使用FedBCD\[137\]用于nn，使用SecureBoost\[46\]用于树。

### 6.2 隐私

fls的一个重要需求是保护用户隐私。这里我们分析了管理者的可靠性。如果经理诚实而不好奇，那么我们就不需要采用任何额外的技术，因为FL框架确保了原始数据不会被交换。如果经理诚实但好奇，那么我们必须考虑到可能的推理攻击。模型参数还可能暴露有关训练数据的敏感信息。可以采用差分隐私\[71，48，147\]向参数中注入随机噪声，也可以使用SMC\[24，87，25\]交换加密参数。如果管理器根本不可信，那么我们可以使用可信执行环境\[44\]来执行管理器中的代码。区块链也是扮演管理者角色的选项\[109\]。

### 6.3 效率

效率是该系统普及的关键因素。要提高效率，最有效的方法就是应对瓶颈。如果瓶颈在于计算，我们可以使用强大的硬件，如gpu\[50\]和tpu\[98\]。如果瓶颈在于通信，可以采用压缩技术\[20，111，183\]来减小通信规模。

### 6.4 自主

一个实际的外联必须考虑当事人的自治权。双方可能在FL过程中退出，特别是在跨设备场景中。因此，制度不能过于依赖单方。它应该容忍一小部分参与者的失败。另外，双方可能是自私的，不愿意分享质量好的模型。激励机制\[101，102\]可以鼓励各方的参与，提高最终的模型质量。

### 6.5 设计指南

我们根据第4节中所示的分类法和设计因素得出了开发FLS的指导原则。

第一步是从实际场景出发，通过对系统方面的分析来确定FL算法。这些方面包括数据划分、联邦规模、通信体系结构和机器学习模型。一旦参与实体固定，联邦的数据分区和规模几乎就固定了。假设用户想要进行FL来改进谷歌键盘预测。在这种情况下，我们有水平数据分区和跨设备联邦场景。谷歌可以提供一个服务器作为FL的管理者，所以我们可以采用集中式的通信架构。因此，FedAvg和FedMA等算法可以满足我们的要求。由于LSTM在单词预测任务上表现良好，我们可以采用专门针对cnn和LSTM设计的FedMA\[208\]算法来训练LSTM模型。

下一步是满足隐私要求。虽然当前的规则没有明确限制模型参数的传输，但如果单个记录非常敏感，则FLS应该保护它们免受推理攻击。例如，医院想要对肺癌的预测任务进行FL。在FLS中，应充分保护患者的病历，并采用不同的保密或安全的多方计算技术。没有隐私保证的FLS非常容易受到攻击。

最后一步是考虑激励机制。如果规章激励各方进行FL，那么FL就不需要激励机制。但是，要设计一个生动活泼的FL，激励机制是鼓励各方参与和贡献的重要因素。激励机制的基本要求是保证当事人贡献越大，得到的奖励就越多。区块链是一个提供稳定和可验证激励的选项\[109，101，102\]。

## 7 案例研究

在本节中，我们将根据我们的分类介绍几个实际的FL应用程序，如表3所示。

![image-20210508102639520](https://gitee.com/zlt_shadow/res/raw/master/images-bed/paper5/table-3.png)

表3：真实联邦系统的需求

### 7.1 移动服务

有许多公司为他们的移动用户提供预测服务，如谷歌Keyboard\[226\]，苹果的表情暗示和QuickType\[198\]。这些服务给用户带来了很多方便。然而，训练数据来自于用户的边缘设备，比如智能手机。如果该公司收集所有用户的数据，并训练一个全局模型，这可能会导致隐私泄露。另一方面，单个用户的数据不足以训练出一个准确的预测模型。FL使这些公司能够在不访问用户原始数据的情况下训练出一个准确的预测模型，这意味着保护了用户的隐私。在fls框架下，用户计算并发送自己的本地模型，而不是原始数据。这意味着谷歌键盘用户可以享受准确预测下一个单词，而不分享他/她的输入历史。如果FLS能够广泛应用于此类预测服务，由于数据总是存储在边缘，因此数据泄漏将大大减少。

在这种情况下，数据通常在数百万台设备上水平分割。因此，单个设备的计算资源和带宽的限制是两个主要问题。此外，还应考虑系统的健壮性，因为用户可以随时加入或离开系统。换句话说，应该为这种预测服务设计一个集中的、跨设备的水平数据FLS。

尽管FLS的基本框架可以以某种方式保护个人隐私，但它可能无法安全抵御推理攻击\[186\]。应该利用一些额外的隐私机制，如差分隐私，以确保个人的无区别性。在这里，安全的多方计算可能并不合适，因为每个设备的计算能力都很弱，无法承担昂贵的加密操作。除了保障用户的隐私外，还应该建立一些激励机制，鼓励用户贡献自己的数据。实际上，这些奖励可以是代金券或额外的服务。

### 7.2 医疗

现代卫生系统需要研究机构、医院和联邦机构之间的合作，以改善国家的卫生保健\[67\]。此外，在面临COVID-19\[7\]等全球卫生紧急情况时，国家间的合作研究至关重要。这些卫生系统的主要目的是训练一种疾病诊断模型。这些诊断模型应该尽可能准确。但是，根据GDPR\[11\]等规定，患者信息不允许转移。在国际合作中，数据的隐私问题更是备受关注。如果不解决隐私问题，合作研究可能会停滞不前，威胁公共健康。此类合作中的数据隐私在很大程度上是基于保密协议的。但毕竟，这种解决方案是建立在“信任”的基础上的，而“信任”并不可靠。FL使合作成为可能，因为它可以在理论上保证隐私，这是可证明的和可靠的。这样一来，每家医院或机构只需共享本地的模型，就能得到准确的诊断模型。

在这种情况下，医疗保健数据被横向和纵向划分：每一方都包含用于特定目的\(例如病人治疗\)的居民的健康数据，但每一方使用的功能是不同的。参与者的数量是有限的，每个参与者通常都有大量的计算资源。换句话说，需要混合分区数据上的私有FLS。其中最具挑战性的问题是如何训练混合分区数据。FLS的设计可能比简单的水平系统更复杂。在医疗保健联盟中，可能没有中央服务器。所以，另一个具有挑战性的部分是去中心化FLS的设计，它也应该对一些不诚实或恶意的方具有健壮性。此外，隐私问题可以通过安全多方计算和差异隐私等附加机制来解决。这种合作在很大程度上是受到监管的推动。

### 7.3 金融

金融联邦会由银行、保险公司等组成。他们经常希望在日常财务操作中进行合作。例如，一些“不良”用户可能会把从另一家银行借的钱打包回一家银行。所有的银行都希望避免这种恶意行为，同时又不泄露其他客户的信息。另外，保险公司也想向银行了解客户的信誉。然而，“好”客户的信息泄露可能会导致客户失去兴趣或引发一些法律问题。

如果我们有一个值得信任的第三方，比如政府，这种合作就可以发生。但是在很多情况下，政府并不参与联邦，或者政府并不总是受信任的。因此，可以引入具有隐私机制的FLS。在FLS中，通过理论证明的隐私机制可以保证每个银行的隐私。

在这种情况下，财务数据通常是垂直分区的，并按用户ID链接。在垂直划分的数据中训练分类器是相当具有挑战性的。一般情况下，训练过程可以分为两部分：隐私保护的记录链接\[201\]和垂直联合训练。第一部分是寻找垂直分区数据之间的联系，这已经得到了很好的研究。第二部分是在不共享各方原始数据的情况下训练关联数据，这仍然是一个挑战。跨竖井和分散的场景在这个联邦中应用。此外，在这种情况下应该采用一些隐私机制，参与者可以受到兴趣的激励。

## 8 愿景

在本节中，我们将展示将来要研究的有趣方向。

### 8.1 异构性

当事人的异构性是FL的一个重要特征。基本上，各方可以在可访问性、隐私需求、对联合的贡献和可靠性方面有所不同。因此，在fls中考虑这些实际问题是很重要的。

#### 8.1.1 动态调度

由于当事人的不稳定性，在学习过程中当事人的数量可能不是固定的。然而，特别是在跨竖井的场景中，许多现有研究中缔约方的数量是固定的，它们没有考虑到有新缔约方加入或现有缔约方离开的情况。该系统应支持动态调度，并具有在参与者数量发生变化时调整策略的能力。有一些研究解决了这个问题。例如，谷歌的系统\[26\]可以容忍设备的退出。区块链\[242\]的出现也为多方学习提供了一个理想的透明平台。需要在这方面作出更多的努力。

#### 8.1.2 不同的隐私限制

很少有研究考虑到fls的隐私异构性，其中各方有不同的隐私要求。现有系统采用技术来保护模型参数或在同一层次上的所有各方的梯度。然而，现实中当事人的隐私限制往往不同。设计一种根据当事人的隐私限制不同对待他们的FLS将是很有趣的。如果我们能最大限度地利用各方的数据，同时不违反他们的隐私限制，那么学习模型应该有更好的性能。异构差分隐私\[10\]可能在这种设置中有用。

#### 8.1.3 聪明的好处

直观地说，如果FLS提供更多信息，那么一方应该从它获得更多信息。一个简单的解决方案是在各方之间达成协议，让一些各方为提供更多信息的其他各方支付费用。需要建立具有代表性的激励机制。

#### 8.1.4 鲁棒性

虽然可以在FL中使用差分隐私来提供保护，以防止潜在的推理攻击，但还有其他危险的攻击，如由恶意方造成的数据中毒和后门攻击。基于这一思路，Gu等人\[79\]提出了一个在可信执行环境中实现模型问责的多方协作学习系统。Ghosh等人\[73\]考虑了拜占庭当事人\(或异常和敌对当事人\)的模型稳健性。另一种可能的方法是区块链\[168，108\]。Preuveneers等人\[168\]提出了一种基于许可区块链的FL方法来监控异常检测机器学习模型的增量更新。

### 8.2 系统开发

为了促进fls的发展，除了详细的算法设计外，还需要从高层次的角度进行研究。

#### 8.2.1 系统架构

与深度学习中控制参数同步的参数服务器一样，FL也需要研究一些常见的系统架构。虽然FedAvg是一个应用广泛的框架，但适用场景仍然有限。例如，如何在无监督的环境下进行联邦学习？另外，除了模型平均之外的其他聚合方法呢?我们想要一个通用的系统架构，为不同的场景提供多种聚合方法和学习算法。

#### 8.2.2 模型市场

FL的一个可能的变体是我们维持一个买卖的模型市场。参与方可以购买模型在本地进行模型聚合。此外，它还可以为其模型提供诸如目标任务等额外信息。这样的设计为联邦带来了更多的灵活性，更容易为组织所接受，因为FL就像几个事务。在这类系统中，对模型进行良好的评价是很重要的。激励机制可能会有所帮助\[219，101，102\]。

#### 8.2.3 基准

随着更多的fls的开发，一个具有代表性的数据集和工作负载的基准对于评估现有系统和指导未来的开发是非常重要的。虽然已有相当多的基准\[30，91，89\]，但在联邦学习研究的实验中还没有广泛使用的基准。我们需要一个具有代表性的数据集、全面的指标和严格的隐私评估的健壮的基准。尽管如此，更多的应用程序和场景是fls成功的关键。

#### 8.2.4 数据生命周期

学习只是联邦系统的一个方面。数据生命周期包括数据创建、存储、使用、共享、归档和销毁等多个阶段。为了整个应用的数据安全和隐私，我们需要在FL语境下发明新的数据生命周期。虽然数据共享显然是重点阶段之一，但fls的设计也影响到其他阶段。例如，数据创建可能有助于准备适合FL的数据和特性。

### 8.3 域内的FL

#### 8.3.1 物联网

随着物联网应用的不断发展，雾计算和边缘计算的安全与隐私问题成为研究的热点。更多细节，读者可以参考一些最近的调查\[191，227，153\]。FL可能是解决数据隐私问题的一种潜在方法，同时仍然提供相当好的机器学习模型\[129，156\]。额外的关键挑战来自计算和能量限制。隐私和安全机制引入了运行时开销。例如，Jiang等人\[95\]利用独立高斯随机投影来提高数据的隐私性，然后训练一个深度网络可能代价太大。需要开发新的资源调度算法，将工作负载转移到具有更高计算能力的节点上。类似的问题也发生在其他环境中，如车对车网络\[178，181\]。

#### 8.3.2 规定

虽然FL可以在不暴露原始数据的情况下实现协作学习，但目前仍不清楚FL如何遵守现有的规定。例如，GDPR对数据传输提出了限制。既然模型和梯度实际上是不够安全的，那么这种限制还适用于模型还是梯度？此外，“解释权”很难执行，因为全局模型是局部模型的平均值。FL模型的可解释性是一个开放性问题\[81，179\]。此外，如果用户想删除其数据，是否应该在没有数据的情况下重新训练全局模型\[75\]？在实际应用中，FL技术与规范还存在一定的差距。我们可以期待计算机科学界和法律界的合作。

## 9 结论

许多人致力于开发联邦学习系统\(fls\)。对现有的fls进行完整的概述和总结是非常重要和有意义的。受到以前联邦系统的启发，我们已经证明了异构性和自治性是实际fls设计中的两个重要因素。此外，我们从六个不同的方面为fls提供了一个全面的分类。在此基础上，对现有的语言系统进行了功能和设计的比较。更重要的是，我们指出了许多机会，从更多的基准测试到区块链等新兴平台的集成。fls将是一个令人兴奋的研究方向，它需要机器学习、系统和数据隐私社区的努力。

## **致谢**

这项工作得到了新加坡教育部AcRF一级拨款\(T1 251RES1824\)、商青青年学者研究基金和教育部二级拨款\(MOE2017-T2-1-122\)的支持。

