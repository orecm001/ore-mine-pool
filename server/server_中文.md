# 服务端

### 谁需要运行ore-mine-pool服务端
大约总算力达到2M H/s以上的矿工，才需要运行ore-mine-pool server。这样你可以建立属于自己的ore挖矿集群。

### 如何运行

```bash
1、创建挖矿钱包，需要使用solana命令,将其放入与挖矿程序同目录下的keypair文件夹下
solana-keygen new --outfile keypair/wallet.json
2、给钱包转入部分sol以支付挖矿gas费用
3、使用ore mine --keypair 钱包文件，来初始化挖矿Proof账户(每个钱包一次即可)
4、后台运行server
nohup ./start.sh > start.log 2>&1 &
5、使用你的woker连接你的server，修改worker启动参数中的 --server-url 为你的server地址
```

### 安全须知
由于我们是闭源项目，使用server端会读取你的server端存储的挖矿钱包。因此你会有资金安全的担忧。

如果你不信任我们 ->  使用我们公开的矿池，这里仅仅提供钱包地址接收奖励即可

如果你有点信任我们 -> 你可以自建server端，定期补充挖矿钱包少量gas费用即可。

如果你充分信任我们 -> 你可以对钱包进行质押ore来获得质押系数，获得更多挖矿奖励。

### 调优
1、修改--priority-fee 来调整挖矿gas费用支出，它不应该低于13000，否则会大幅影响上链成功率。

2、修改--base-jito-tip 来调整jito费用，目前计算规则是（任务难度-14) * 2000 + base-jito-tip。

3、修改--min-difficulty 来调整最低难度要求，低于这个难度的hash，客户端将不再上报。

4、钱包数量，至少2个钱包你才能享受到无缝的woker计算(在一个钱包进入提交结果阶段时，另一个在计算阶段)，建议是3-5个，不应该超过5个。

5、如果你选择质押，又资金有限，建议使用更少的钱包来集中质押，享受更高的质押系数。维持平均难度28以上。这样你会放弃一些低难度上链(如28以下)，但是28以上会获得更高的质押系数。

6、当前最高质押用户在270-441之间变动，为了使质押更有效率，建议单个钱包质押在270以下。

7、rpc需要使用进行了sol质押的rpc供应商，以便使用支持[swQoS](https://www.helius.dev/blog/stake-weighted-quality-of-service-everything-you-need-to-know)的rpc服务，增加上链速度与成功率。如helius、triton。

### 费率

1、15% (我们的费用全部质押进质押池，当质押池质押总量达到4000时，会下调到10%)

2、矿工自行承担gas和rpc费用

如果您觉得收费偏高，欢迎使用我们公开池和质押池，质押池享受费用后105%的收益！
