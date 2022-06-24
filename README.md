# Chain-Features
A repository to use as a simple point of reference for specific Chain Features to be configured in Nethereum. Ideally chain features will be an endpoint from the Node, see: https://ethereum-magicians.org/t/rfc-signer-feature-detection-over-json-rpc/5773, but when and if this is done, a mapping can be done to the existing service. 

A Chain Feature provides information of the capabilities of a Chain, for example does it support 1559? 
Currently that is the main configuration check, but this can be extended with specific smart contract addresses like Proof Of Humanity, MultiCall, WETH Gateway Address in L2 and so on.

### How do I add a chain capabilites for others to view?

Just create an issue and it will be added to the Repo.

### How does it work in Nethereum?
The current chain feature in Nethereum is defined as this:

```csharp
 public class ChainFeature
    {
        public BigInteger ChainId { get; set; }
        public bool SupportEIP1559 { get; set; } = true;
        public string ChainName { get; set; }
    }
```
To register a feature just simply add them here.

```csharp
ChainFeaturesService.Current.UpsertChainFeature(
      new ChainFeature()
      {
          ChainName = "Nethereum Test Chain",
          ChainId = 444444444500,
          SupportEIP1559 = false
      });
```

### Full example in Nethereum
This test demontrates how to set your custom chain properties, to flag if 1559 is supported or not. If not ChainFeature is configured, it will default to 1559, if the GasPrice is not set, or LegacyMode enabled.

```csharp
[Fact]
        public async void ShouldSendTrasactionBasedOnChainFeature()
        {

            var web3 = _ethereumClientIntegrationFixture.GetWeb3();
            ChainFeaturesService.Current.UpsertChainFeature(
                new ChainFeature()
                {
                    ChainName = "Nethereum Test Chain",
                    ChainId = 444444444500,
                    SupportEIP1559 = false
                });

            var toAddress = "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe";
            var tranHash = await web3.Eth.TransactionManager.SendTransactionAsync(new TransactionInput()
            {
                From = EthereumClientIntegrationFixture.AccountAddress,
                To = toAddress,
                Value = new HexBigInteger(100),
            }
            );

            var tran = await web3.Eth.Transactions.GetTransactionByHash.SendRequestAsync(tranHash);
            Assert.NotNull(tran.TransactionHash);
            Assert.Null(tran.MaxFeePerGas);
            Assert.Null(tran.MaxPriorityFeePerGas);
            Assert.NotNull(tran.GasPrice);

            ChainFeaturesService.Current.UpsertChainFeature(
                new ChainFeature()
                {
                    ChainName = "Nethereum Test Chain",
                    ChainId = 444444444500,
                    SupportEIP1559 = true
                });

            var tranHash2 = await web3.Eth.TransactionManager.SendTransactionAsync(new TransactionInput()
            {
                From = EthereumClientIntegrationFixture.AccountAddress,
                To = toAddress,
                Value = new HexBigInteger(100),
            }
            );

            var tran2 = await web3.Eth.Transactions.GetTransactionByHash.SendRequestAsync(tranHash2);
            Assert.NotNull(tran2.TransactionHash);
            Assert.NotNull(tran2.MaxFeePerGas);
            Assert.NotNull(tran2.MaxPriorityFeePerGas);
            Assert.NotNull(tran2.GasPrice);

            ChainFeaturesService.Current.TryRemoveChainFeature(444444444500);
            //Should default to 1559 when not feature is set

            var tranHash3 = await web3.Eth.TransactionManager.SendTransactionAsync(new TransactionInput()
            {
                From = EthereumClientIntegrationFixture.AccountAddress,
                To = toAddress,
                Value = new HexBigInteger(100),
            }
           );

            var tran3 = await web3.Eth.Transactions.GetTransactionByHash.SendRequestAsync(tranHash3);
            Assert.NotNull(tran3.TransactionHash);
            Assert.NotNull(tran3.MaxFeePerGas);
            Assert.NotNull(tran3.MaxPriorityFeePerGas);
            Assert.NotNull(tran3.GasPrice);
        }

```
