    struct order {
      address orderOwner;
      uint rate;
      uint amount;
      uint date;
    } 
   
    struct done {
      address orderOwner;
      address fillOwner;
      uint fillRate;
      uint fillAmount;
      uint fillDate;      
    }
	
    struct market {  
      bool exists;
      address tokenPair;
      mapping(uint => order) orders;
      mapping(uint => done) dones;
    }

    struct token {
      address tokenBase;
      string name;
      string symbol;
      uint decimals;
      uint like;
      uint dislike; 
      mapping(address => market) markets;
    }
	
    mapping(uint => address) public index;
    mapping(address => token) public tokens;
    mapping(address => bool) public exists;
  
