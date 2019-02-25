# Give an ITO (Initial Token Offering)

We already know what is a `NEP-5` token how to [implement](https://github.com/neo-ngd/NEO-Tutorial/blob/steven/smartContract/What_is_nep5.md) your NEP-5 token. The NEP-5 token is used as an asset which can used to transfer between users. However, only issue such a `NEP-5` token is not profitable for issuer because you have to link this asset to outside world manually. In NEO, there is a way for your to trade between `NEP-5` token  and global asset such as NEO.

ITO stands for Initial Token Offering. With this process, you can digitize or tokenize your asset and make it publicly available through the internet. This means you can start a business, company or project with any asset value. Via ITO, you generate digital coins or tokens which represent your asset. Consecutively, you can electronically transfer these coins or tokens.

The ITO standard in NEO is based on the NEP-5 which we implemented before. In addition to those methods and properties already defined in the NEP-5, in the ITO, there are several new emthods and property should add in.

## Timestamp
It is worth noting that every ITO has limited amount of time and number of tokens. Therefore, in the ITO contract, we should define the start time of ITO and the finish time of ITO. Besides this period, the ITO can not be invoked successfully. 
```csharp
//start time of ito. Which is Monday, August 14, 2017 4:00:00 PM
private const int ico_start_time = 1502726400;
//end time of ito. Which is : Monday, August 28, 2017 4:00:00 PM
private const int ico_end_time = 1503936000;
```

Inside the contract , we add a `CurrentSwapRate` rate function. The function judge if the current block time is inside the predefined ITO period. In side the function, it get the block time from the `Blockchain.GetHeader` and the `BlockChain.GetHeight` API. Those API can query the information of the block and header directly. More API can be found [here](https://docs.neo.org/en-us/sc/reference/api/neo.html).

```csharp
// The function CurrentSwapRate() returns the current exchange rate
// between ico tokens and neo during the token swap period
private static ulong CurrentSwapRate()
{
    // factor is detemined by the decimal, which is a constant. The raate means 1 NEO => 1000 NEP5
    const ulong basic_rate = 1000 * factor;
    const int ico_duration = ico_end_time - ico_start_time;
    BigInteger total_supply = Storage.Get(Storage.CurrentContext, "totalSupply").AsBigInteger();
    if (total_supply >= total_amount) return 0;
    uint now = Blockchain.GetHeader(Blockchain.GetHeight()).Timestamp;
    int time = (int)now - ico_start_time;
    if (time < 0){
        return 0;
    } else if (time > ico_duration){
        return 0;
    } else{
        return basic_rate;
    }
}
```
> In this code snippet of `CurrentSwapRate`, only the basic rate is retured, however, in the real life ITO, due to the different time period, the program can return different swap rate.

## MintToken

The `MintToken` method is the most important method in the ITO contract (which also has more things to learn).  In the `MintToken` method
```csharp
  Transaction tx = (Transaction)ExecutionEngine.ScriptContainer;
```