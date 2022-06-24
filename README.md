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
