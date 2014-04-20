---
layout: post
title: Setup testnet for bitcoin on Mac OSX
category: Tech
tags: [Bitcoin]
---


### 1. Install Bitcoin-Qt Client

1) Download Mac version from [the download page](https://bitcoin.org/en/download) of official website.

2) Double click the dmg file and drag into `/Application` folder.



### 2. Configuration file

Before you start it, you need to configure some options such as `testnet`, `rpcuser`, `rpcpassword`.

1) Create the data content directory for Bitcoin if it doesn’t exist.

    mkdir -p ~/Library/Application\ Support/Bitcoin

2) Add `bitcoin.conf` in the data content directory. Here is a sample bitcoin configuration file:

    testnet=1
    server=1
    rpcuser=YOUR_RPC_USER
    rpcpassword=YOUR_RPC_PASSWORD
    rpctimeout=30
    rpcport=8332

For more available options in `bitcoin.conf`, check it [here](https://en.bitcoin.it/wiki/Running_Bitcoin#Command-line_arguments)



### 3. Run Bitcoin-Qt

To start Bitcoin-Qt, you only need to click the application icon in `/Applications`. If you have enabled `testnet` option in `bitcoin.conf`, the application icon should be green instead of normal orange color. When Bitcoin-Qt is running, you can see a `testnet3` folder in `~/Library/Application\ Support/Bitcoin`, it is the same structure as its parent directory(for the real bitcoin network):

    ├── blocks
    ├── chainstate
    ├── database
    ├── db.log
    ├── debug.log
    ├── peers.dat
    └── wallet.dat

For the details of this structure, check it [here](https://en.bitcoin.it/wiki/Data_directory#Directory_Contents).

All transaction logs can be seen in `debug.log`. When you received bitcoins, you will get something like this:

    2014-04-20 03:11:05 receive version message: /Satoshi:0.9.1/: version 70002, blocks=225797, us=221.232.86.145:18535, them=23.239.17.57:18333, peer=23.239.17.57:18333
    2014-04-20 03:11:05 Added time data, samples 13, offset -1 (+0 minutes)
    2014-04-20 03:11:05 nTimeOffset = +0  (+0 minutes)
    2014-04-20 03:11:48 AddToWallet eb204331b354e0977e337b24fb0b145c9935776bf985ab2ae283742a7f3839b7  new



### 4. Retrieve testnet bitcoins

1) To retrieve testnet bitcoins, you need to get a receiving address first with Bitcoin-Qt.

2) Go to a [testnet faucet](http://faucet.xeno-genesis.com/), enter the receiving address and bitcoin amount, then summit the form.

3) After the transaction is finished, you can check it [here](http://tbtc.blockr.io/) with the receiving address.



### 5. Check

In general, it will spend a few minutes to synchronize. After the testnet blockchain is downloaded, you can see it in your balance.

You can also check it with command line by using JSON RPC:

    curl --user YOUR_RPC_USER --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "getinfo", "params": [] }' -H 'content-type: text/plain;' http://127.0.0.1:8332/
    Enter host password for user 'root':
    {"result":{"version":90100,"protocolversion":70002,"walletversion":60000,"balance":37.38825610,"blocks":225803,"timeoffset":-1,"connections":12,"proxy":"","difficulty":1.00000000,"testnet":true,"keypoololdest":1397888847,"keypoolsize":101,"paytxfee":0.00000000,"errors":""},"error":null,"id":"curltest"}

**References**:

* [https://en.bitcoin.it/wiki/Testnet](https://en.bitcoin.it/wiki/Testnet)
* [https://en.bitcoin.it/wiki/Running_Bitcoin](https://en.bitcoin.it/wiki/Running_Bitcoin)
* [https://en.bitcoin.it/wiki/Data_directory](https://en.bitcoin.it/wiki/Data_directory)