# 研究记录15-研究过程中的问题总结


一些以前理解上出现偏差的地方，这里做校正或完善。研究过程中发现的问题，可作为下一步研究方向的，也总结在这里。

<!--more-->

## 1. 理解上的偏差

### 中心化

物联网由于入网设备的迅速增加导致中心化的服务器压力增大，企业维护成本增加，但区块链并不能算很好的解决方案。服务器的压力主要分两方面，一是存储海量数据的压力，二是海量交易处理的压力，对于前者，区块链的存储更加昂贵，对于后者，区块链的吞吐量不如传统的中心化系统。另外，区块链虽然可以解决传统中心化系统的单点瓶颈和故障，但这是分布式系统的固有属性，其他分布式系统也可以，现有的各种容灾方案已经很完善，可以应对各种情况。

区块链的优势应当是提供参与各方之间的信任，提高系统的安全性。可以用于减少各企业间的交接成本，减少欺诈风险，追溯出问题的地方等。另外，区块链和中心化的云存储不是相互对立的，两者可以同时使用，即用云存储数据，同时用区块链存储哈希保证数据的完整性和准确性。

### 数据隐私

区块链很难提供身份隐私，它的公钥账户机制是一种伪匿名，可以被追溯到真实身份，尽管可以通过密码学各种混淆方式尽力实现真正的匿名，但容易沦落为洗钱的温床，不受监管的区块链无法得到发展和推广。

我们提到区块链的隐私应该更多的指数据的隐私，有两层含义：

1. 用户拥有对自己数据的完全控制权，包括知晓被谁使用和如何被使用，同时可以随时撤回授权；
2. 存在区块链中的数据由于是公开的，可能被竞争对手利用从而获得竞争优势，应当尽力阻止这一点。

Quorum 的隐私管理器主要是解决第二点，让数据只能被指定的账户看到。

### 智能工厂

在寻找区块链和 IIoT 的结合场景时出于某种已忘记的原因选择了智能工厂。当时的重点放在了这种场景和区块链的可结合性分析上，虽然在 [区块链和智能工厂](https://shuzang.github.io/2019/blockchain-for-smart-factory/) 一文阐明了这种可结合性，但缺乏实质的设计和实现方案。

当时有一个半成品方案，但这个方案是现在的访问控制方案的原型，访问控制和这一方向的关系是：访问控制是区块链和智能工厂场景可结合性的一个方面。

### 平台和共识选择

选择 Quorum 时关注的几个方面包括：共识、吞吐量、对智能合约的支持、对隐私管理的支持、构建和开发的难易程度、社区的活跃程度（决定了有问题是否可以及时得到解决）。

不选 Ethereum 是因为它使用 PoW 共识且吞吐量较小，不选 Hyperledger 是因为它没有隐私管理。当时坚定的认为平台本身提供的隐私保护比自己重新提出一个隐私保护要好的多，现在有所动摇。大量的论文采用了自己的密码学算法，也许这些方案有更好的性能，只是易用性不够高而已。

不选 Hyperledger 还有一个原因是对海量数据存储的支持，现在认为这并不重要，大多数的存储方案将数据存在链下，数据的哈希存在链上这种方式，通常对区块链本身没有特别的要求。

### 基准测试工具

对各区块链平台和共识进行基准测试的工具和论文较少，当时想自己提出这样一个测试方案并实现，更多的出于保证工作量足够的目的。没有做下去是因为缺乏足够的认识和能力，也缺乏足够的时间和团队协作编写一个针对各个区块链平台和共识的测试框架，事实上，这件事只是做的人少，不是没有人做。

注：平台和共识选择是需要一些数据支撑的，比如吞吐量等，目前依然缺乏这方面的数据（指自己得到的而不是论文或网上看来的）。有时间可以借助这些工具自己做一下测试。

### 访问控制

对场景的验证要求有一个可用的硬件平台， 网关、传感器等，实际上并没有，这种硬件平台对方案的验证不具备必要性，更多的是验证区块链终端软件在物联网终端设备上可运行的能力或对设备造成的压力。

我们假设的是供应链或其它场景中每一个参与方都拥有自己的验证者节点，但是具体为每一方分配多少验证者节点可以达到公平到现在还是无法确定。

IBFT 共识中，超过2/3的节点串通就可以控制整个共识过程，少于2/3但大于1/3的节点串通可以导致共识永远无法达成。因此，多数人攻击是一个潜在的安全问题，[Roberto Saltini](https://arxiv.org/pdf/1901.07160.pdf) 分析了IBFT的安全性并给出了改进建议，最好看一看这个建议。

新的节点加入时，需要分配一定数量的 Ether 用于之后的合约部署或合约调用操作，我们曾设想的方案是，在合约中预存大量的 Ether，每个设备新入网时自行调用获取一定数目的余额，或者注册自身时由合约自动发送一定数量的余额。这种方案是不可行的，因为无论是自行调用合约还是注册自身的操作，都会改变合约状态，也就是说这个操作本身就需要有一定的 Ether 才能完成，这是一个鸡生蛋蛋生鸡的问题，唯一的办法是通过其他的方式发送余额，比如新的验证者、管理者入网时，由固定的某个账户给与一定数量的初始 Ether，新的设备入网时，由它的管理者为该账户分配一定数量的 Ether。

联盟里本身就带有准入机制，要理解这个机制和我们额外实现的访问控制之间的区别。

进行静态检查避免重复注册可以算一种工作内容。

Remix 编译时的一些 warning 可以总结一下作为一部分实现上的改进

绝大部分设计和合约实现的优化都是为了减少 Gas 消耗，但是要注意，我们的方案由于实现了更多的功能，不可避免的又增加了一部分消耗，真正评估时应评价在区块链的持续增长过程中总的 Gas 消耗。另外，由于 Quourm 区块链交易不消耗 Gas，因此这方面的优化其实没有在 Eethereum 中实现重要。

> [研究记录8](https://shuzang.github.io/2019/summary-of-problems-and-optimization-considerations-about-prototype-system/) 一文有大量的想法记录，值得多次查看，很多想法都忘了但是却很有意义。

架构设计上的一些想法与减少 Gas 消耗无关，比如 Quorum 区块链的选择，分层结构的设计。

## 2. 未来的研究方向

目前的方案只下沉到了网关这一级，IoT设备只是用一个虚拟的区块链账户表示，返回的访问结果仅是一个字符串，网关往下的部分还有很多工作可以做

### 数据访问和命令执行

物联网终端设备的类型有很多种情况，对于 Always-on 的设备，比如智能家居中的温湿度传感器、摄像头等，收到数据访问或命令执行请求时，可以实时的返回结果，但对于不是一直在线的设备，比如广域网通信的 Zigbee设备，可能是周期性的启动侦听网关的指令，或者定时启动主动联系网关，还比如短距离通信的蓝牙，智能手环或智能手表只有在软件打开后才会主动进行一次数据同步。这些情况中，网关收到来自区块链的访问请求后，无法得到实时的结果返回，导致访问控制执行的时间具有不确定性。可选的解决方案有：

1. 对于收集环境数据的传感器，设备与网关保持传统的协作模式，收集的数据存储在本地或云中，不同的是，每次数据提交的时间和数据的哈希等元数据信息要记录到区块链中（存储合约等），当发生数据访问时，将根据请求寻找链中的元数据信息，进而找到真实数据返回，亦或者直接返回元数据信息。另外，在网关中缓存最近一次提交的数据，可以有效减小访问操作完成的时间（时间局部性原理）
2. 对于执行命令的执行器，可以转为异步模式处理，请求和返回的结果都用队列进行存储。

### 结果可靠性的保证

在网关和网关所管理的设备构成的集合体中，网关处于一种中心化的地位，因此，如果网关由于受到攻击或其它的原因不可靠，那么网关所管理的所有设备也会变得不可靠，因为网关具有拒绝执行相关命令或者返回错误的执行结果的能力。这里如何判定网关的可信程度，可以利用传统的异常检测算法，同时，访问决策的逻辑中也可以加入对访问者归属网关状态的判断，网关的不可靠也可以标识为一种恶意行为，从而拒绝该次访问。

在网关可靠的情况下，结果的返回同样具有不确定性，是使用链下普通的通信方式返回结果，还是利用区块链返回结果（存储压力），仍需考虑。

### 访问控制的优化

我们曾提出过两个优化方式，虽然由于各种原因并没有实现，但是现在看具有一定的可行性。

1. 通过定义设备的优先级，在未定义设备访问控制策略时自动的根据优先级判定；
2. 当前的授权是即时的，应当可以自定义授权时间，使得在某段时间内重新访问不必重新发起访问（参考内存置换算法中访问的时间局部性原理）。

另外，诸如权限转移（有权限者将权限级联授权给其它用户）和自动属性发现机制等也值得继续研究

### 通信问题

> P. Danzi, A. E. Kalor, C. Stefanovic and P. Popovski, "Analysis of the Communication Traffic for Blockchain Synchronization of IoT Devices," *2018 IEEE International Conference on Communications (ICC)*, Kansas City, MO, USA, 2018, pp. 1-7, doi: 10.1109/ICC.2018.8422485.

一直记着上面这篇论文，想接着它的思路做，包括：

1. 不同的无线和有线接入技术对区块链同步的影响（损耗和延迟）；
2. 设备休眠对区块链同步的影响；
3. 设备硬件平台的异构性对区块链软件的影响等。

该方向的论文较少，最后没有进一步研究下去的原因是这些内容更多的是关于通信的内容，对各种接入技术和通信性能评估方法都不够了解。

另外，本身小范围内设备通信，利用点对点、网状拓扑或星型拓扑延迟都还可以接受，但到区块链中走了一圈之后，通信延迟突然增加不少

### 区块链压缩

成千上万的访问控制交互，对区块链存储造成的压力，在区块链不断延伸的过程中，应当可以对前面许多过时的或无效的信息进行压缩，甚至在上链阶段就添加某些源信息用于之后搜索进行压缩。思路启发自以下论文

>T. Kim, J. Noh and S. Cho, "SCC: Storage Compression Consensus for Blockchain in Lightweight IoT Network," 2019 IEEE International Conference on Consumer Electronics (ICCE), Las Vegas, NV, USA, 2019, pp. 1-4, doi: 10.1109/ICCE.2019.8662032.

### D2D访问

基于token的访问控制，用D2D等技术辅助某些终端问题的解决，思路启发自下面的论文，想接着做下去的

> V. A. Siris, D. Dimopoulos, N. Fotiou, S. Voulgaris and G. C. Polyzos, "Trusted D2D-Based IoT Resource Access Using Smart Contracts," 2019 IEEE 20th International Symposium on "A World of Wireless, Mobile and Multimedia Networks" (WoWMoM), Washington, DC, USA, 2019, pp. 1-9, doi: 10.1109/WoWMoM.2019.8793041.


