# ore-mine-pool

ore-mine-pool is a mining pool implemented for orev2, making it easier for miners to mine. Compared to ore-cli, it is easier to use and more efficient.
Our current output of about 665 / day, about 46% of the total network capacity.

## Communication

[discord](https://discord.gg/PjpqgmJkkY)
[dune](https://dune.com/oreminepool/ore-mine-pool-dashboard)

## Worker Usage

```bash
1. git clone https://github.com/xintai6660707/ore-mine-pool.git
2. cd ore-mine-pool
3. chmod +x start.sh
4. chmod +x ore-mine-pool-linux
5. Modify the worker-wallet-address in start.sh to your wallet address and ensure the wallet has the corresponding ore AssociatedToken address.
6. Modify the threads in start.sh to the number of threads of your CPU.
6. If you don't have an ore wallet address, you can use ./ore-mine-pool-linux create-associated-token --keypair "your wallet private key address" --priority-fee 20000 to create an ore associated account (consider the security of your private key, you can also create it yourself).
7. nohup ./start.sh > start.log 2>&1 & // Start the worker in the background
8. tail -f worker.log // View worker logs

To pause the task:
pkill -f start.sh
pkill -f ore-mine-pool


monitor all program record use:
./ore-mine-pool-linux  monitor   --rpc-ws-url  wss://xxxxxx

The address you want to monitor, by default, monitors all transactions throughout the program. You can fill in your wallet address to monitor only your wallet:
./ore-mine-pool-linux  monitor   --rpc-ws-url  wss://xxxxxx --monitor-address  your-wallet-address

save record as csv ,for excel analysis:
./ore-mine-pool-linux  monitor   --rpc-ws-url  wss://xxxxxx   --csv_mode

```
## Working Principle


We run the pool server and use multiple wallets to get mining tasks. The Worker fetches the current task with the lowest difficulty from the server every 10 seconds, performs 10 seconds of computation, and submits the highest difficulty answer obtained. The server records the wallet of the submitter with the highest difficulty answer. When the task needs to be submitted at 55 seconds, the highest difficulty answer will be submitted to the blockchain, and the miner fee will be collected.  

##### Why use ore-mine-pool-worker

## Higher computational efficiency

When using ore-cli for computation, mining is performed for about 55 seconds, then submission takes 1 second to several minutes, and mining can only continue after submission. If each submission takes 30 seconds (which is normal under ordinary priority fees), the efficiency is only 55/55+30=64%. Using ore-mine-pool-worker, each computation takes 10 seconds, and the submission of the answer and fetching the next task takes less than 0.1 seconds, resulting in an efficiency of 10/10+0.1=99%, which is a 1.54 times efficiency improvement compared to ore-cli.  

## No interval penalty

When using ore-cli for computation, submission times of 20 to several minutes are common. orev2 has a unique interval penalty where if the task is computed for 55 seconds and submitted within 10 seconds, the full reward is obtained. Submissions within 10-70 seconds result in a halved reward, and each additional minute further halves the reward. Using ore-cli, submission times of 20 to several minutes often result in halved or even zero rewards. ore-mine-pool-worker only computes the result, and we use high priority fees and the best RPC to ensure full rewards, resulting in approximately double the earnings.  

## Better bus selection

In ore, there are 8 buses (each with 1/8 of the reward capacity). ore-cli submits rewards to a randomly selected bus, but there is an imbalance among the buses. If the randomly selected bus has zero rewards, the submission will result in zero rewards. ore-mine-pool-worker selects the best bus for submission (viewing the optimal bus on-chain), ensuring full rewards. The efficiency improvement is not quantified here.  

## Staking rewards

In orev2, miners with stakes receive a coefficient increase of 1-2 times for reward submissions. When using ore-cli, due to low stakes, the coefficient is close to 1. ore-mine-pool-worker collects miner fees and stakes them, resulting in increased earnings with higher stakes, approaching a 2 times efficiency improvement.  

## No gas fees

Submitting rewards with ore-cli incurs gas fees, which can consume over 50% of the rewards in many cases. Using ore-mine-pool-worker, the gas fees are covered by us, resulting in a 2 times earnings improvement.  
## Easier maintenance

We handle blockchain interactions, and the worker only needs to fetch tasks, compute, and submit answers, without dealing with complex blockchain interactions, making it easier to maintain.  

## Security

Only the wallet public key is required, with no risk of private key leakage.  

## Transparency (Anti-Fraud)

We will record the hash of each task's final computation as an event. The worker will provide an event subscription feature, allowing you to view detailed information for each transaction, including the final hash, worker wallet address, amount, difficulty, etc. This proves that the high-difficulty hash you computed is indeed rewarded to you, ensuring no fraudulent behavior. Unlike other pools, we are fully transparent.

## Total earnings improvement

In total: 1.54 * 2 * 2 * 2 = 12.32 times efficiency improvement. (Currently, without staking rewards, the efficiency improvement is 6.16 times)  

## Fees


pool-fee: 15% (we cover gas fees and server maintenance)  



## Links
ore-mine-pool on-chain program address: [Feei2iwqp9Adcyte1F5XnKzGTFL1VDg4VyiypvoeiJyJ](https://solscan.io/account/Feei2iwqp9Adcyte1F5XnKzGTFL1VDg4VyiypvoeiJyJ)

program-fee account: [link](https://solscan.io/account/4756i3S8EPsTvKjVvUaCbP9JF8JpjQW7AmXEZnGeZDhp)


## TODO

Windows version support(done)

Browser support

Worker mining earnings subscription

Multi-server fault tolerance, switch to another server in case of failure(done)

On-chain program outputs the hash of the current task submission, allowing users to verify no fraudulent behavior(done)

Dune charts(done)
