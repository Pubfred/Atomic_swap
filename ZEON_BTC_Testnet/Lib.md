# Launch atomic swap as library

Before setting up atomic swap code, see [node configuration](Node-conf.md) manual.

## Configure
All configurations appears in main function. 
Customize it according to your situation and environment.

### Example
Let's imagine that we are making atomic swap between Zeon and Bitcoin Testnet blockchains. 
Configuration parameters are listed below. There are two parts of configuration - for you (`A`) and for your partner (`B`). 
Each of participants should set both but in different order.
In this example `A` sends Zeon and receives Bitcoins Testnet. `B` is vise versa.


##### Configuration for `A`
`A`'s IP address and port to connect with `B` via socket:
```java
selfData.inetAddress(new InetSocketAddress("ddd.ddd.ddd.ddd", dddd));
```

IP address and port of Zeon node RPC:
```java
selfData.nodeAddress(new InetSocketAddress("127.0.0.1", 21944));
```

Username and password for Zeon node RPC:
```java
selfData.nodeLogin("user");
selfData.nodePassword("password");
```

Port number for ZMQ notification from Zeon node:
```java
selfData.notificationPort(1112);
```

Amount of Zeons to send (in satoshi's):
```java
selfData.amount(Coin.valueOf(1000000000));
```

`A`'s Zeon private key (uncompressed, HEX formatted):
```java
selfData.myKey(ECKey.fromPrivate(new BigInteger("<private_key_here>", 16), false));
```

`B`'s Zeon public key (uncompressed, HEX formatted):
```java
selfData.otherKey(ECKey.fromPublicOnly(new BigInteger("<public_key_here>", 16).toByteArray()));
```

Zeon worker class instance to work with Zeon blockchain:
```java
selfData.worker(ZeonWorker.instance());
```

Amount of confirmations to assume that Zeon transaction is mature and amount of confirmations (CSV) to make refund transaction back:
```java
selfData.confirmations(2);
selfData.csv(3);
```

Partner data configuration is similar except another cryptocurrency blockchain aspects.

##### Configuration for `B`
`B`'s IP address and port to connect with `A` via socket:
```java
partnerData.inetAddress(new InetSocketAddress("ddd.ddd.ddd.ddd", dddd));
```

IP address and port of Bitcoin Tesnet node RPC:
```java
partnerData.nodeAddress(new InetSocketAddress("127.0.0.1", 8332));
```

Username and password for Bitcoin Testnet node RPC:
```java
partnerData.nodeLogin("user");
partnerData.nodePassword("password");
```

Port number for ZMQ notification from Bitcoin testnet node:
```java
partnerData.notificationPort(1113);
```

Amount of Bitcoins to receive (in satoshi's):
```java
partnerData.amount(Coin.valueOf(1000000000));
```

`A`'s Bitcoin private key (uncompressed, HEX formatted):
```java
partnerData.myKey(ECKey.fromPrivate(new BigInteger("<private_key_here>", 16), false));
```

`B`'s Bitcoin public key (uncompressed, HEX formatted):
```java
partnerData.otherKey(ECKey.fromPublicOnly(new BigInteger("<public_key_here>", 16).toByteArray()));
```

Bitcoin worker class instance to work with Bitcoin blockchain:
```java
partnerData.worker(BitcoinWorker.instance());
```

Amount of confirmations to assume that Bitcoin transaction is mature and amount of confirmations (CSV) to make refund transaction back:
```java
partnerData.confirmations(2);
partnerData.csv(3);
```

## RUN

Application implements Finite State Machine (FSM) based on reactive approach.

To run the FSM you should configure selfData and partnerData parameters first. 
You may look at [App.java] file for example. But use only one case during configuration.

After that create an instance of AtomicSwapFSM:
```java
final FSM<TxState<TxStatus>> fsm = AtomicSwapFSM.create(
	selfData, partnerData,
	true,
	5, TimeUnit.SECONDS,
	5, TimeUnit.SECONDS,
	1000, "AtomicSwapFSM Event", "AtomicSwapFSM-Task-%d"
);
```

And finally start the atomic swap process:
```java
fsm.start();
```