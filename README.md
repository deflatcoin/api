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

# Serverless Database Frontend

Require Metamask
https://against.network/testnet/ Select Ropsten Network (testnet)

Contract Details: https://github.com/deflatcoin/ethereum-contracts/blob/master/serverlessDexContract.md

CSS Block https://github.com/deflatcoin/decentralization/blob/master/css-block.css

HTML Container https://github.com/deflatcoin/decentralization/blob/master/html-container.html

ABI Block https://github.com/deflatcoin/decentralization/blob/master/abi-block.js

<b>Getting the token list from the database JS</b>

<pre>
  function listTokens(container) {
    document.getElementById(container).innerHTML += "<option selected title='SELECT' value='SELECT'>SELECT TOKEN</option>";
    exchangeContract.indexCount((error,indexCount) => {
      var html;
      var i;
      for (i = 1; i <= indexCount; i++) {         
            exchangeContract.index.call(i, function (err,addr) {
              exchangeContract.tokens.call(addr, function (err,token) {    
                if (baseDefault.toUpperCase() == token[0].toUpperCase()) {
                    document.getElementById(container).innerHTML += "<option selected title="+token[0]+" value="+token[0]+">"+token[1].toUpperCase()+"</option>";
                    listTokenMarkets();
                } else {
                  document.getElementById(container).innerHTML += "<option title="+token[0]+" value="+token[0]+">"+token[1].toUpperCase()+"</option>";
                }                         
              });            
            });           
      }       
    });
  }
</pre>

<b>Getting the Market list from the database JS</b>
<pre>
  function listTokenMarkets() {
    var html;
    var i;      
      clearFields();
      addr = document.getElementById("tkaddress").value;  	  
      baseName = document.getElementById("tkaddress").options[document.getElementById("tkaddress").selectedIndex].text;	   
      exchangeContract.tokens.call(addr, function (err,token) {                      
          baseDecimals = token[3];
         baseSymbol = token[2];  		 
      });    	       
      getLikesBase();	      
      document.getElementById("baseCoinTitle").innerText = baseName;
      document.getElementById('baseCoin').value = addr;
      document.getElementById("tkpairaddress").innerHTML = "";      
      if (addr != "SELECT") {      
        getMyBalance(addr,web3.eth.accounts[0], 'myBaseBalance');  
        exchangeContract.tokens.call(addr, function (err,token) {
           baseDecimals = token[3]; 
           baseSymbol = token[2];			
           marketCount = token[6];  
           if (token[6] == 0) {
               document.getElementById("tkpairaddress").innerHTML = "<option title='SELECT' value='SELECT'>NO MARKET</option>";
               document.getElementById("tkpairaddress").selectedIndex = 1;
           }      
           for (i=1; i <= marketCount; i++) {
             document.getElementById("tkpairaddress").innerHTML = "<option selected title='SELECT' value='SELECT'>SELECT MARKET</option>";
             exchangeContract.getPairByIndex.call(addr, i, function (err,market) {         
               exchangeContract.tokens.call(market[0], function (err,token) { 
                  if (pairDefault.toUpperCase() == token[0].toUpperCase()) {
                    document.getElementById("tkpairaddress").innerHTML += "<option selected title='"+token[0]+"' value='"+token[0]+"'>"+token[1].toUpperCase()+"</option>"; 
                    listOrders(true); 
                  } else {
                    document.getElementById("tkpairaddress").innerHTML += "<option title='"+token[0]+"' value='"+token[0]+"'>"+token[1].toUpperCase()+"</option>";
                  }                                     
               });    
             });         
           }
        });
     } else {
       document.getElementById("tkpairaddress").innerHTML = "<option title='SELECT' value='SELECT'>NO MARKET</option>";
       document.getElementById("tkpairaddress").selectedIndex = 1;
     }          
  }
</pre>

<b>Getting order list JS</b>

<pre>
  function listOrders(clear) {
    var html;
    var i;
    loading = true;
    asyncElmDetected = 0;
    asyncElmLoaded = 0;
      if (clear) {
         clearFields(); 
         setCookie("baseDefaultAddr",document.getElementById("tkaddress").value,10);
         setCookie("pairDefaultAddr",document.getElementById("tkpairaddress").value,10);        
      } else {
        document.getElementById("typeSell").innerHTML = '';
        document.getElementById("typeBuy").innerHTML = '';
      }
      addr = document.getElementById("tkaddress").value; 
	  baseName = document.getElementById("tkaddress").options[document.getElementById("tkaddress").selectedIndex].text;	
      pairAddr = document.getElementById("tkpairaddress").value;
      pairName = document.getElementById("tkpairaddress").options[document.getElementById("tkpairaddress").selectedIndex].text;	  
	  exchangeContract.tokens.call(pairAddr, function (err,token) {                      
          pairDecimals = token[3];
          pairSymbol = token[2];  		 
          getAllowanceAccount(pairAddr,web3.eth.accounts[0],'approvePairValue');
      });
      getLikesPair();   	
      getMyBalance(addr,web3.eth.accounts[0], 'myBaseBalance'); 
      getMyBalance(pairAddr,web3.eth.accounts[0], 'myPairBalance');   
      document.getElementById("btnApproveBase").innerText = baseName;
      document.getElementById("btnApprovePair").innerText = pairName;     
      document.getElementById("btnApproveBase").title = 'Approve value to '+baseName;
      document.getElementById("btnApprovePair").title = 'Approve value to '+pairName;   
      document.getElementById('baseExplorerLink').href = 'https://'+ropstenDomain+'etherscan.io/address/'+addr;
      document.getElementById('pairExplorerLink').href = 'https://'+ropstenDomain+'etherscan.io/address/'+pairAddr;
      document.getElementById("sellHeader").innerHTML = '<td>THEY OFFER '+baseName+'</td><td class="rateCol"><span style="cursor:pointer;" onclick="sortOrders(\'typeSell\',false);sortOrders(\'typeBuy\',false)">RATE/SORT</span></td><td class="fillCol"><span style="cursor:pointer;" onclick="listOrders(false)">REFRESH</span></td><td>THEY WANTS '+pairName+'</td><td>FUNDS: '+baseName+'</td><td>ALLOWANCE '+baseName+'</td>';
      document.getElementById("buyHeader").innerHTML =  '<td>THEY OFFER '+pairName+'</td><td class="rateCol"><span style="cursor:pointer;" onclick="sortOrders(\'typeSell\',false);sortOrders(\'typeBuy\',false)">RATE/SORT</span></td><td class="fillCol"><span style="cursor:pointer;" onclick="listOrders(false)">REFRESH</span></td><td>THEY WANTS '+baseName+'</td><td>FUNDS: '+pairName+'</td><td>ALLOWANCE '+pairName+'</td>';
      if ((addr != "SELECT") && (pairAddr != "SELECT")) {         
        getAllowanceAccount(addr,    web3.eth.accounts[0],'approveBaseValue');                        
        document.getElementById('pairSymbolSell').innerText = 'SEND '+baseName;
        document.getElementById("pairSymbolSellPlaced").innerText = 'TO GET '+pairName;  
        document.getElementById('pairSymbolBuy').innerText  = 'SEND '+pairName; 
        document.getElementById("pairSymbolBuyPlaced").innerText = 'TO GET '+baseName;  	
        exchangeContract.getPairByAddr(addr, pairAddr,  function (err,market) {
           ordersCount = market[0];
		   ordersCountOld = parseInt(ordersCount);
           donesCountOld  = parseInt(market[1]);
           asyncElmDetected = asyncElmDetected+(ordersCount*3);
           for (i=1; i <= ordersCount; i++) {
               exchangeContract.getOrders(addr, pairAddr, i, function (err, orders) {
                     if (orders[1] == web3.eth.accounts[0]) {
                        fillLbl = 'YOU';
                     } else {
                        fillLbl = 'FILL';
                     }                             
                     document.getElementById("typeSell").innerHTML += '<tr title="Tradeable order"><td title="'+orders[1]+'">'+(orders[3]/(10**baseDecimals)).toFixed(decimalPlaces)+'</td><td class="rateCol">'+(orders[2]/(10**9)).toFixed(9)+'</td><td class="fillCol"><button class="fillBtn" id="fillBtna'+i+'" onclick="showFillOrder('+orders[0]+', '+orders[2]+', '+orders[3]+', '+baseDecimals+', '+pairDecimals+', 1)">'+fillLbl+'</button></td><td>'+(((orders[3]/(10**baseDecimals))/orders[2])*(10**9)).toFixed(decimalPlaces)+'</td><td id="apairbalance'+orders[0]+'">'+getBalance(addr,orders[1],orders[0],'a',1,orders[3])+'</td><td id="apairallowance'+orders[0]+'">'+getAllowance(pairAddr,addr,orders[1],exchangeAddr,orders[0], orders[1],'a',1,baseDecimals,orders[3])+'</td></tr>';
               });   
           }
        });    
        exchangeContract.getPairByAddr(pairAddr, addr,  function (err,market) {
           ordersCount = market[0];
           asyncElmDetected = asyncElmDetected+(ordersCount*3);
           if (market[2]) {                
           }
           for (i=1; i <= ordersCount; i++) {
               exchangeContract.getOrders(pairAddr, addr, i, function (err, orders) {
                     if (orders[1] == web3.eth.accounts[0]) {
                        fillLbl = 'YOU';
                     } else {
                        fillLbl = 'FILL';
                     }                               
                     document.getElementById("typeBuy").innerHTML += '<tr title="Tradeable order"><td title="'+orders[1]+'">'+(orders[3]/(10**pairDecimals)).toFixed(decimalPlaces)+'</td><td class="rateCol">'+(1/(orders[2]/(10**9))).toFixed(9)+'</td><td class="fillCol"><button class="fillBtn" id="fillBtnb'+i+'" onclick="showFillOrder('+orders[0]+', '+orders[2]+', '+orders[3]+', '+baseDecimals+', '+pairDecimals+', 0)">'+fillLbl+'</button></td><td>'+(((orders[3]/(10**pairDecimals))/orders[2])*(10**9)).toFixed(decimalPlaces)+'</td><td id="bpairbalance'+orders[0]+'">'+getBalance(pairAddr,orders[1],orders[0],'b',0,orders[3])+'</td><td id="bpairallowance'+orders[0]+'">'+getAllowance(addr,pairAddr,orders[1],exchangeAddr,orders[0], orders[1],'b',0,pairDecimals,orders[3])+'</td></tr>';
               });   
           }
        });                   
     } else {
       document.getElementById("tkpairaddress").innerHTML = "<option title='SELECT' value='SELECT'>NO MARKET</option>";
     }
     listOrdersDones();  
  }
</pre>

<b>Getting done list JS</b>

<pre>
  function listOrdersDones() {
    var html;
    var i;
      document.getElementById("typeSellDones").innerHTML = '';
      document.getElementById("typeBuyDones").innerHTML = '';
      addr = document.getElementById("tkaddress").value; 
      pairAddr = document.getElementById("tkpairaddress").value;
      document.getElementById("sellHeaderDones").innerHTML = '<td>'+baseName+'</td><td><span>RATE</span></td><td>DATE</td>';
      document.getElementById("buyHeaderDones").innerHTML =  '<td>'+pairName+'</td><td><span>RATE</span></td><td>DATE</td>';
      if ((addr != "SELECT") && (pairAddr != "SELECT")) {   
        exchangeContract.getPairByAddr(addr, pairAddr,  function (err,market) {
           ordersCount = market[1];
           document.getElementById('donesCount1').innerText = ordersCount;
           start = ordersCount-4; // last 5
           if (start < 1) {start = 1} 
           for (i=start; i <= ordersCount; i++) {
               exchangeContract.getDones(addr, pairAddr, i, function (err, orders) {                              
                    document.getElementById("typeSellDones").innerHTML += '<tr><td>'+(orders[2]/(10**pairDecimals)).toFixed(9)+'</td><td>'+(orders[4]/(10**9)).toFixed(5)+'</td><td>'+orders[3]+'</td></tr>';
               });   
           }
        });    
        exchangeContract.getPairByAddr(pairAddr, addr,  function (err,market) {
           ordersCount = market[1];
           document.getElementById('donesCount2').innerText = ordersCount;
           start = ordersCount-4; // last 5
           if (start < 1) {start = 1} 
           for (i=start; i <= ordersCount; i++) {
               exchangeContract.getDones(pairAddr, addr, i, function (err, orders) {                           
                     document.getElementById("typeBuyDones").innerHTML += '<tr><td>'+(orders[2]/(10**baseDecimals)).toFixed(9)+'</td><td>'+(orders[4]/(10**9)).toFixed(5)+'</td><td>'+orders[3]+'</td></tr>';
               });   
           }
        });                   
     }
  }
</pre>
