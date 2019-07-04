# DECENTRALIZED API, TOOLS & DATABASE 

Decentralized exchange API

Proprietary clusters **

High response speed and high availability ***

** proprietary structures are not resistant to censorship.

*** is limited to the number of clusters and parallel networks that the company can maintain. 

Volunteer clusters & networks (last stage)

Censorship-resistant and very high availability ecosystem.

# Under development

On Chain Database

- No server;
- Anyone can add an Ethereum token;
- Anyone can add a market to any token;
- Like / dislike voting system to assess reputation. One vote per wallet;
- The contract does not censor any token, but the gateway owner can use the reputation as a filter if he so desires;
- The cost of warehousing per transaction can be offset by low brokerage fees;
- No tokens custody is required. Great for anyone who wants to keep their orders for a long time, waiting for the asset to reach the desired price (Holder traders);
-No key or signature is stored in the contract;
- Future expandable to support a payment system that accepts any Ethereum token.

# Create an AGAINST release gateway

Copy the page template https://github.com/deflatcoin/decentralization/blob/master/against-partner-release.html and in contractrelease.gatewayTransaction change address param by your wallet address, in it you will receive the equivalent of 1% of the sale in AGAINST, for example if a transaction is made in the amount of 1,000,000 AGAINST you will receive 10,000 AGAINST in your wallet.

      $('#getReleaseBtn').click(() => {
        const paymentAddress = '0xfe601b539d93B5aa286ad6eDaCE145c4E80A12A3'
        const amountEth = parseInt(((document.getElementById('qtdAGAINST').value/price))*10000000)/10000000
        contractrelease.gatewayTransaction('<0xB82D... your wallet address here>',{
          to: paymentAddress,
          value: web3.toWei(amountEth, 'ether'),
          gas: 200000,          
        }, (err, transactionId) => {
          if  (err) {
            console.log('Transaction failed', err)
            $('#statust').html('Transaction failed')
          } else {
            console.log('Transaction successful', transactionId)
            $('#statust').html('Transaction successful: '+transactionId+'')
          }
        })
      })
    }
    
Open a free account on an ipfs gateway, such as pinata.cloud or temporal.cloud (among others) and upload and pin the page, Done, now just share the generated link.

You can also use the link to include page content on your site using the html embed tag

WP integrated example: https://deflatcoin.io/embed-release-gateway-demo/

Full Page Example: https://deflatcoin.io/full-page-gateway-demo/

IPFS Link: https://gateway.pinata.cloud/ipfs/QmQoM511owRMt6PX2BV91yCHLoMaJsoEd2A1Pg9LJ7j7xN

 
<b>Contract transaction example:</b> 

https://etherscan.io/tx/0xe4090f210f080e29eab5591607d1d0bcbddc89a6ee188b26d6ce35182b95e6dc

0.001 ETH send from buyer to seller

500000 AGAINST send seller to buyer

5 AGAINST send seller to gateway owner

Important for the end user: in the METAMASK window, before authorizing, make sure that it is interacting with the contract:

<b>0xfe601b539d93B5aa286ad6eDaCE145c4E80A12A3</b>
