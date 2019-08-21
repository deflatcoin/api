# AGAINST Project

The AGAINST Project aims to develop a fully decentralized ecosystem for crypto assets, including currency, exchange and site.

# New DEX Frontend
The frontend is new decentralized Ethereum Token Exchange, with changes that differentiate the need of a central host and / or domain, being able to IPFS or localhost implementation. This does not prevent anyone from making the gateway available in a domain if they wish. The important thing is the freedom to choose the implementation you find most appropriate.

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
https://against.network/newdex/ Now working on MainNet & Ropsten Network (testnet)

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

<b>Starting App JS</b>

<pre>
    const urlParams = new URLSearchParams(window.location.search);
    startBaseDefault = "0xe1E0DB951844E7fb727574D7dACa68d1C5D1525b";
    startPairDefault = "0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2";

    var baseName = "";
    var pairName = "";
	
    function link(domain) {
        var loc = window.location.pathname;
        var scriptfolder = loc.substring(0, loc.lastIndexOf('/'));
        document.write("<a href='https://"+domain+scriptfolder+"' target='_blank'>"+domain+"</a>");      
    }

    function setCookie(cookie_name, cookie_value, expire_in_days) {
             var cookie_expire = "";
             if (expire_in_days != null) {
	                  var expire = new Date();
	                  expire.setTime(expire.getTime() + 1000*60*60*24*parseInt(expire_in_days));
	                  cookie_expire = "; expires=" + expire.toGMTString();
             }
             document.cookie = escape(cookie_name) + "=" + escape(cookie_value) + cookie_expire;               
    }

    function getCookie(cookie_name) {
            if (!document.cookie.match(eval("/" + escape(cookie_name) + "=/"))) { 
                return false;
            }
            return unescape(document.cookie.replace(eval("/^.*?" + escape(cookie_name) + "=([^\\s;]*).*$/"), "$1"));
    }

    if (urlParams != "") {
       baseDefault = urlParams.get('baseAddr');
       pairDefault = urlParams.get('pairAddr');        
    } else {
       cookieBase = getCookie("baseDefaultAddr");
       cookiePair = getCookie("pairDefaultAddr");
       if (!cookieBase) {
           baseDefault = startBaseDefault; 	
          } else {            
            baseDefault = cookieBase;          
       }
       if (!cookiePair) {
            pairDefault = startPairDefault; 	
          } else {            
            pairDefault = cookiePair;          
       }       
    }

	function orderWatch() {
	    if ((addr != "SELECT") && (pairAddr != "SELECT")) {		   
           exchangeContract.getPairByAddr(addr, pairAddr,  function (err,market) {
             ordersCountNew = market[0];
             donesCountNew = market[1];  			  
			 if (parseInt(ordersCountNew) != parseInt(ordersCountOld)) {
			     ordersCountOld = parseInt(ordersCountNew);
                 donesCountOld = parseInt(donesCountNew);
                 if (!loading) {
			         listOrders(false);
                 } 
             } else {
                     if (parseInt(donesCountNew) != parseInt(donesCountOld)) {
			            donesCountOld = parseInt(donesCountNew);
                        if (!loading) {
			              listOrders(false);
                        }
			         }
             }
		  });
	    }
	}
	
    function startEx() {
          web3.version.getNetwork((err, netId) => {
             if (netId == 1) {
                ropsten = false;
                opstenDomain = "";
                printStatus(false, 'MainNet Selected!');
                exchangeAddr = "0x2dcf69b59c2301Dd2bC632F7F9f3EB7b93b98E31";
	            wrapAddr = "0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2";
                startBaseDefault = "0xe1E0DB951844E7fb727574D7dACa68d1C5D1525b";
                startPairDefault = "0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2";
                exchangeContract = web3.eth.contract(exchangeABI).at(exchangeAddr);
                wrapContract = web3.eth.contract(wrapABI).at(wrapAddr); 
                setCookie("baseDefaultAddr", startBaseDefault, 10);
                setCookie("pairDefaultAddr", startPairDefault, 10);
             } else if (netId == 3) {
                ropsten = true;
                ropstenDomain = "ropsten.";
                printStatus(false, 'Ropsten Network Selected!');
                exchangeAddr = "0xd85c6c8FAD288AB905747149799223Fe554686dD";
	            wrapAddr = "0xb603cEa165119701B58D56d10D2060fBFB3efad8";
                startBaseDefault = "0x7Da44D4E0856b337f38e50b342237DC86D9032E3";
                startPairDefault = "0x3d74C695B17505606480dae9EF3DCd6756C728DA";
                exchangeContract = web3.eth.contract(exchangeABI).at(exchangeAddr);
                wrapContract = web3.eth.contract(wrapABI).at(wrapAddr); 
                setCookie("baseDefaultAddr", startBaseDefault, 10);
                setCookie("pairDefaultAddr", startPairDefault, 10);
             } else {
                printStatus(false, 'Network not suported, select MainNet or Ropsten Network!');
                ropsten = true;
                ropstenDomain = "notsuported.";
                exchangeAddr = "0xd85c6c8FAD288AB905747149799223Fe554686dD";
	            wrapAddr = "0xb603cEa165119701B58D56d10D2060fBFB3efad8";
                startBaseDefault = "0x7Da44D4E0856b337f38e50b342237DC86D9032E3";
                startPairDefault = "0x3d74C695B17505606480dae9EF3DCd6756C728DA";
                exchangeContract = web3.eth.contract(exchangeABI).at(exchangeAddr);
                wrapContract = web3.eth.contract(wrapABI).at(wrapAddr); 
                setCookie("baseDefaultAddr", startBaseDefault, 10);
                setCookie("pairDefaultAddr", startPairDefault, 10);
             }
             document.getElementById('accountExplorerLink').href = 'https://'+ropstenDomain+'etherscan.io/address/'+web3.eth.accounts[0];        
             account = web3.eth.accounts[0];
             accountInterval = setInterval(function() {
                if (web3.eth.accounts[0] !== account) {
                    account = web3.eth.accounts[0];
                    location.reload();
                }
             }, 100);	
             currentNetwork = netId;
             networkInterval = setInterval(function() {                
                   web3.version.getNetwork((err, netId) => {
                      if (netId != currentNetwork) {
                         currentNetwork = netId;
                         location.reload();
                      }
                   });
                   if ((asyncElmDetected > 0) && (asyncElmLoaded >= asyncElmDetected)) {
                      asyncElmDetected = 0;
                      asyncElmLoaded = 0;  
                      loading = false; 
                      sortOrders('typeSell',false);
                      sortOrders('typeBuy',false);                      
                   }				    
             }, 500);
             watchInterval = setInterval(function() {
                if (!loading) {
                    orderWatch();
                }
             }, 6000);	
             document.getElementById('helpLink').href = 'about.html';
             document.getElementById("myBaseBalance").value = 0;
             document.getElementById("myPairBalance").value = 0;
             getMyBalanceLabel(wrapAddr,web3.eth.accounts[0],'wrapBalance');  
             getEthBalanceLabel(web3.eth.accounts[0],'ethBalance');
             listTokens("tkaddress");
             getFees();    
             document.getElementById('contractExplorerLink').href = 'https://'+ropstenDomain+'etherscan.io/address/'+exchangeAddr;
          });			
     }	
	
    window.addEventListener('load', async () => {
      resetLikes(); 
      document.getElementById("btnSell").disabled = true;
      document.getElementById("btnSell").style.background = 'silver'; 
      document.getElementById("btnBuy").disabled = true;
      document.getElementById("btnBuy").style.background = 'silver'; 
      if (window.ethereum) {
        window.web3 = new Web3(ethereum);
        try {
          await ethereum.enable();
          startEx();	  
        } catch (err) {
          document.getElementById('statust1').innerText = 'User denied account access';
        }
      } else if (window.web3) {
        window.web3 = new Web3(web3.currentProvider);
        startEx(); 
      } else {
          document.getElementById('statust1').innerText = 'No Metamask (or other Web3 Provider) installed';
      }
    })
</pre>

<b>Token Register</b>

- Contract Side:

<pre>
    function registerToken(address _token) public payable {
       require((msg.sender == owner) || (msg.value >= registerFee), "Register Fee Very Low");
       erc20 refToken = erc20(_token);
       if (!exists[_token]) {            
            indexCount = indexCount+1;
            index[indexCount] = _token; 
            tokens[_token].tokenBase = _token;  
            tokens[_token].name = refToken.name();		
            tokens[_token].symbol = refToken.symbol();
            tokens[_token].decimals = refToken.decimals();			
            tokens[_token].likesCount = 0;
            tokens[_token].dislikesCount = 0;
            tokens[_token].marketsCount = 0; 		
            exists[_token] = true;            
       }	             
    }
</pre>

1- address: Token to register;

2- refToken: Get data from token contract;

3- indexCount: Increase token count;

4- index[]: Define tokenCount as token index;

5- name, symbol and decimal: from refToken contract;

6- liskesCount, dislikes and marketsCount: Start with 0;

7- exists: Formal, always true; 

- Web Side:

<pre>
    function sendRegisterToken() {
       token = document.getElementById('registerBaseCoin').value;
       exchangeContract.registerToken(token, {value:registerFee, gas:250000,}, (err, transactionId) => {
          printStatus(err, transactionId);        
       }); 
    }
</pre>

<b>createMarket</b>

- Contract Side:

<pre>
    function createMarket(address _token, address _tokenPair) public payable {
      require(msg.value >= openMarketFee, "Open Market Fee Very Low");
      require(exists[_token] && exists[_tokenPair],"token or tokenPair not listed");     
      require(!tokens[_token].markets[_tokenPair].exists,"Market already exists");
      require(tokens[_token].tokenBase != _tokenPair,"Not allowed token = tokenPair");
      tokens[_token].marketsCount = tokens[_token].marketsCount+1;
      tokens[_token].marketIndex[tokens[_token].marketsCount] = _tokenPair;
      tokens[_token].markets[_tokenPair].tokenPair = _tokenPair;
      tokens[_token].markets[_tokenPair].ordersCount = 0;
      tokens[_token].markets[_tokenPair].donesCount = 0;
      tokens[_token].markets[_tokenPair].exists = true;
    }
</pre>

1- _token and _tokenPair: Pair market;

2- openMarketFee: Fee value for market creation;

3- marketsCount: Increase count of markets of base token;

4- marketIndex: Make marketsCount as index of current market;

5- ordersCount and donesCount: Set vars with 0;

6- exists: Formal always true after creation;

- Web Side:

<pre>
    function sendCreateMarket() {
       baseToken = document.getElementById('baseCoin').value;
       pairToken = document.getElementById('pairAddr').value;
       exchangeContract.createMarket(baseToken, pairToken, {value: marketFee, gas:200000,}, (err, transactionId) => {
         printStatus(err, transactionId);        
       }); 
    }
</pre>
