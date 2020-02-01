pragma solidity ^0.4.25;

library SafeMath
{
	function mul(uint256 a, uint256 b) internal pure
	returns (uint256)
	{
		uint256 c = a * b;

		assert(a == 0 || c / a == b);

		return c;
	}

	function div(uint256 a, uint256 b) internal pure
	returns (uint256)
	{
		// assert(b > 0); // Solidity automatically throws when dividing by 0
		uint256 c = a / b;
		// assert(a == b * c + a % b); // There is no case in which this doesn't hold
		return c;
	}

	function sub(uint256 a, uint256 b) internal pure
	returns (uint256)
	{
		assert(b <= a);

		return a - b;
	}

	function add(uint256 a, uint256 b) internal pure
	returns (uint256)
	{
		uint256 c = a + b;

		assert(c >= a);

		return c;
	}
}

contract ERC20Basic
{
	function totalSupply() public view returns (uint256);
	function balanceOf(address who) public view returns (uint256);
	function transfer(address to, uint256 value) public returns (bool);
	event Transfer(address indexed from, address indexed to, uint256 value);
}

contract ERC20 is ERC20Basic
{
	function allowance(address owner, address spender) public view returns (uint256);
	function transferFrom(address from, address to, uint256 value) public returns (bool);
	function approve(address spender, uint256 value) public returns (bool);
	event Approval(address indexed owner, address indexed spender, uint256 value);
}

contract Ownable
{
	address public owner;

	event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);

	constructor() public
	{
		owner = msg.sender;
	}

	modifier onlyOwner()
	{
		require(msg.sender == owner);
		_;
	}

	function transferOwnership(address newOwner) public onlyOwner
	{
		require(newOwner != address(0));
		emit OwnershipTransferred(owner, newOwner);
		owner = newOwner;
	}
}

contract FreezableToken is Ownable
{
	event FrozenFunds(address target, bool frozen);

	mapping (address => bool) frozenAccount;

	function freezeAccount(address target) public onlyOwner
	{
		frozenAccount[target] = true;
		emit FrozenFunds(target, true);
	}

	function unFreezeAccount(address target) public onlyOwner
	{
		frozenAccount[target] = false;
		emit FrozenFunds(target, false);
	}

	function frozen(address _target) view public returns (bool)
	{
		return frozenAccount[_target];
	}
}

contract BasicToken is ERC20Basic, FreezableToken
{
	using SafeMath for uint256;

	string public constant name = "UnitedToken";
	string public constant symbol = "UCT";
	uint8 public decimals = 18;
	uint256 public totalSupply = 700000000e18;

	mapping(address => uint256) balances;

	function totalSupply() public view returns (uint256)
	{
		return totalSupply;
	}

	function _transfer(address _from, address _to, uint256 _value) internal
	{
		require(!frozenAccount[_from]);
		require(!frozenAccount[_to]);
		require(_to != address(0));
		require(balances[_from] >= _value);
		require(balances[_to].add(_value) > balances[_to]);

		balances[_from] = balances[_from].sub(_value);
		balances[_to] = balances[_to].add(_value);
		emit Transfer(_from, _to, _value);
	}

	function transfer(address _to, uint256 _value) public returns (bool)
	{
		_transfer(msg.sender, _to, _value);
		return true;
	}

	function balanceOf(address _owner) public view returns (uint256 balance)
	{
		return balances[_owner];
	}
}

contract StandardToken is ERC20, BasicToken
{
	using SafeMath for uint256;

	mapping (address => mapping (address => uint256)) internal allowed;

	function transferFrom(address _from, address _to, uint256 _value) public returns (bool)
	{
		require(!frozenAccount[_from]);
		require(!frozenAccount[_to]);
		require(_to != address(0));
		require(_value <= balances[_from]);
		require(_value <= allowed[_from][msg.sender]);

		balances[_from] = balances[_from].sub(_value);
		balances[_to] = balances[_to].add(_value);
		allowed[_from][msg.sender] = allowed[_from][msg.sender].sub(_value);
		emit Transfer(_from, _to, _value);
		return true;
	}

	function approve(address _spender, uint256 _value) public returns (bool)
	{
		allowed[msg.sender][_spender] = _value;
		emit Approval(msg.sender, _spender, _value);
		return true;
	}

	function allowance(address _owner, address _spender) public view returns (uint256)
	{
		return allowed[_owner][_spender];
	}

	function increaseApproval(address _spender, uint _addedValue) public returns (bool)
	{
		allowed[msg.sender][_spender] = allowed[msg.sender][_spender].add(_addedValue);
		emit Approval(msg.sender, _spender, allowed[msg.sender][_spender]);
		return true;
	}

	function decreaseApproval(address _spender, uint _subtractedValue) public returns (bool)
	{
		uint oldValue = allowed[msg.sender][_spender];
		if (_subtractedValue > oldValue) {
			allowed[msg.sender][_spender] = 0;
		} else {
			allowed[msg.sender][_spender] = oldValue.sub(_subtractedValue);
		}
		emit Approval(msg.sender, _spender, allowed[msg.sender][_spender]);
		return true;
	}
}

contract Pausable is Ownable
{
	event EPause();
	event EUnpause();

	bool public paused = true;

	modifier whenPaused()
	{
		require(paused);
		_;
	}

	modifier whenNotPaused()
	{
		require(!paused);
		_;
	}

	function pause() public onlyOwner
	{
		paused = true;
		emit EPause();
	}

	function unpause() public onlyOwner
	{
		paused = false;
		emit EUnpause();
	}

	function isPaused() view public returns(bool)
	{
		return paused;
	}

	function pauseInternal() internal
	{
		paused = true;
		emit EPause();
	}

	function unpauseInternal() internal
	{
		paused = false;
		emit EUnpause();
	}
}

contract PausableToken is StandardToken, Pausable
{
	function transfer(address _to, uint256 _value) public whenNotPaused returns (bool)
	{
		return super.transfer(_to, _value);
	}

	function transferFrom(address _from, address _to, uint256 _value) public whenNotPaused returns (bool)
	{
		return super.transferFrom(_from, _to, _value);
	}

	function approve(address _spender, uint256 _value) public whenNotPaused returns (bool)
	{
		return super.approve(_spender, _value);
	}

	function increaseApproval(address _spender, uint _addedValue) public whenNotPaused returns (bool success)
	{
		return super.increaseApproval(_spender, _addedValue);
	}

	function decreaseApproval(address _spender, uint _subtractedValue) public whenNotPaused returns (bool success)
	{
		return super.decreaseApproval(_spender, _subtractedValue);
	}
}

contract VestableToken is PausableToken
{
	using SafeMath for uint256;

	struct VestingDetail
	{
        address beneficiary;
        uint256 startDate;
        uint256 cliffDate;
        uint256 durationSec;
        uint256 released;
        uint256 fullyVestedAmount;
        bool isRevokable;
        bool revoked;
    }

    // for now. Ask and change this
    address vestingTokenWallet = 0x7f7c0205acE513E20cd40B5918e0794657B87b3E;
    mapping (address => VestingDetail) vestingDetails;
    mapping (address => bool) alreadyVested;

    // address[] public vestorAccounts;

	// emitted when new beneficiary added
	event VestedTokenGranted(address indexed beneficiary, uint256 startDate, uint256 cliffDate, uint256 durationSec, uint256 fullyVestedAmount, bool isRevokable);

	// emitted when vesting revoked - all pending tokens are given out, no further vesting done
	event VestedTokenRevoked(address indexed beneficiary);

	// emitted when tokens released
	event VestedTokenReleased(address indexed beneficiary, uint256 amount);

	function grantVestedTokens(address beneficiary, uint256 fullyVestedAmount, uint256 startDate, uint256 cliffSec, uint256 durationSec, bool isRevokable) public onlyOwner returns(bool) // 0 indicates start "now"
	{
	    require(beneficiary != address(0));
	    require (!alreadyVested[beneficiary]);
	    require(!frozen(beneficiary));
	    require(durationSec >= cliffSec);
	    // add check for fullyVestedAmount + totalVestedTillNow <= amountAvailableAtAddress

	    uint256 _startDate = startDate;
	    if (_startDate == 0)
	    {
	    	_startDate = now;
	    }

	    uint256 cliffDate = _startDate.add(cliffSec);

        vestingDetails[beneficiary] = VestingDetail(beneficiary, _startDate, cliffDate, durationSec, 0, fullyVestedAmount, isRevokable, false);
        alreadyVested[beneficiary] = true;

	    emit VestedTokenGranted(beneficiary, _startDate, cliffDate, durationSec, fullyVestedAmount, isRevokable);
	    return true;
  }

	// lets admin remove a beneficiary vesting schedule
	function revokeVesting(address beneficiary) public onlyOwner returns (bool)
	{
		require(beneficiary != address(0));
		require (vestingDetails[beneficiary].isRevokable == true);

		releaseVestedTokens(beneficiary);
		vestingDetails[beneficiary].revoked = true;
		alreadyVested[beneficiary] = false;

		emit VestedTokenRevoked(beneficiary);
		return true;
	}

	function releaseVestedTokens(address beneficiary) public returns (bool)
	{
		require(beneficiary != address(0));
		require(!frozen(beneficiary));
		require(vestingDetails[beneficiary].revoked == false);

		uint256 unreleased = releasableAmount(beneficiary);

		if (unreleased == 0)
		{
			return true;
		}

		vestingDetails[beneficiary].released = vestingDetails[beneficiary].released.add(unreleased);
		_transfer(vestingTokenWallet, beneficiary, unreleased);
		emit VestedTokenReleased(beneficiary, unreleased);
		return true;
	}

	function releasableAmount(address beneficiary) public view returns (uint256)
	{
		return vestedAmount(beneficiary).sub(vestingDetails[beneficiary].released);
	}

	function vestedAmount(address beneficiary) public view returns (uint256)
	{
		uint256 totalBalance = vestingDetails[beneficiary].fullyVestedAmount;

		if (block.timestamp < vestingDetails[beneficiary].cliffDate)
		{
			return 0;
		}
		else if (block.timestamp >= vestingDetails[beneficiary].startDate.add(vestingDetails[beneficiary].durationSec))
		{
			return totalBalance;
		}
		else
		{
			return totalBalance.mul(block.timestamp.sub(vestingDetails[beneficiary].startDate)).div(vestingDetails[beneficiary].durationSec);
		}
	}

	// TODO : add a view only fucntion to get vesting schedule of a beneficiary
}

contract UnitedCrowdsale is VestableToken
{
	using SafeMath for uint;

	uint256 DEC = 10 ** uint256(decimals);
	uint256 public buyPrice = 1000000000000000000 wei;

	uint public stage = 0;
	uint256 public weisRaised = 0;
	uint256 public tokensSold = 0;

	// @dev : review this
	uint public ICOdeadLine = 1533996414; // ICO end time - Sunday, 1 July 2018, 00:00:00.

	mapping (address => uint256) public deposited;
	mapping (address => uint256) public whitelist;

	modifier afterDeadline {
		require(now > ICOdeadLine);
		_;
	}

	// @dev : review this
	uint256 public constant softcap = 85000000e18;
	// @dev : review this
	uint256 public constant hardcap = 420000000e18;

	bool public softcapReached;
	bool public refundIsAvailable;
	bool public burned;

	event SoftcapReached();
	event HardcapReached();
	event RefundsEnabled();
	event Refunded(address indexed beneficiary, uint256 weiAmount);
	event CrowdSaleFinished(string info);
	event Burned(address indexed burner, uint256 amount);

	struct Ico {
		uint256 tokens;             // Tokens in crowdsale
		uint startDate;             // Date when crowsale will be starting, after its starting that property will be the 0
		uint endDate;               // Date when crowdsale will be stop
		uint8 discount;             // Discount
		uint8 discountFirstDayICO;  // Discount. Only for first stage ico
	}

	Ico public ICO;

	function confirmSell(uint256 _amount) internal view
	returns(bool)
	{
		if (ICO.tokens < _amount) {
			return false;
		}

		return true;
	}

	function countDiscount(uint256 amount) internal view
	returns(uint256)
	{
		uint256 _amount = (amount.mul(DEC)).div(buyPrice);
		require(_amount > 0);

		if (1 == stage) {
			_amount = _amount.add(withDiscount(_amount, ICO.discount));
		}
		else if (2 == stage) {
			_amount = _amount.add(withDiscount(_amount, ICO.discount));
		}
		else if (3 == stage) {
			if (now <= ICO.startDate + 1 days) {
				_amount = _amount.add(withDiscount(_amount, ICO.discountFirstDayICO));
			} else {
				_amount = _amount.add(withDiscount(_amount, ICO.discount));
			}
		}
		else if (4 == stage) {
			_amount = _amount.add(withDiscount(_amount, ICO.discount));
		}

		return _amount;
	}

	function changeDiscount(uint8 _discount) public onlyOwner
	returns (bool)
	{
		ICO = Ico (ICO.tokens, ICO.startDate, ICO.endDate, _discount, ICO.discountFirstDayICO);
		return true;
	}

	function changeRate(uint256 _numerator, uint256 _denominator) public onlyOwner
	returns (bool success)
	{
		if (_numerator == 0) _numerator = 1;
		if (_denominator == 0) _denominator = 1;

		buyPrice = (_numerator.mul(DEC)).div(_denominator);

		return true;
	}

	function crowdSaleStatus() internal constant
	returns (string)
	{
		if (1 == stage) {
			return "Private sale";
		}
		else if(2 == stage) {

			return "Pre-ICO";
		}
		else if (3 == stage) {
			return "ICO first stage";
		}
		else if (4 == stage) {
			return "ICO second stage";
		}
		else if (5 >= stage) {
			return "feature stage";
		}

		return "there is no stage at present";
	}

	function paymentManager(address sender, uint256 value) internal
	{
		uint256 discountValue = countDiscount(value);
		require(confirmSell(discountValue));

		sell(sender, discountValue);
		deposited[sender] = deposited[sender].add(value);
		weisRaised = weisRaised.add(value);
		tokensSold = tokensSold.add(discountValue);

		if ((tokensSold >= softcap) && !softcapReached) {
			softcapReached = true;
			emit SoftcapReached();
		}

		if (tokensSold == hardcap) {
			pauseInternal();
			emit HardcapReached();
			emit CrowdSaleFinished(crowdSaleStatus());
		}
	}

	function sell(address _investor, uint256 _amount) internal
	{
		ICO.tokens = ICO.tokens.sub(_amount);
		_transfer(this, _investor, _amount);
		emit Transfer(this, _investor, _amount);
	}

	function startCrowd(uint256 _tokens, uint _startDate, uint _endDate, uint8 _discount, uint8 _discountFirstDayICO) public onlyOwner
	{
		require(_tokens * DEC <= balances[this]);

		ICO = Ico (_tokens * DEC, _startDate, _startDate + _endDate * 1 days , _discount, _discountFirstDayICO);
		stage = stage.add(1);
		unpauseInternal();
	}

	function transferWeb3js(address _investor, uint256 _amount) external onlyOwner
	{
		sell(_investor, _amount);
	}

	function addToWhitelist(address[] _investorsArray) external onlyOwner
	{
		uint arrayLength = _investorsArray.length;
		for (uint i=0; i<arrayLength; i++)
		{
			whitelist[_investorsArray[i]] = 1;
		}
	}

	function removeFromWhitelist(address[] _investorsArray) external onlyOwner
	{
		uint arrayLength = _investorsArray.length;
		for (uint i=0; i<arrayLength; i++)
		{
			whitelist[_investorsArray[i]] = 0;
		}
	}

	function withDiscount(uint256 _amount, uint _percent) internal pure returns (uint256)
	{
		return (_amount.mul(_percent)).div(100);
	}

	function enableRefund() public afterDeadline
	{
		require(!softcapReached);

		refundIsAvailable = true;
		emit RefundsEnabled();
	}

	function getMyRefund() public afterDeadline
	{
		require(refundIsAvailable);
		require(deposited[msg.sender] > 0);

		uint256 depositedValue = deposited[msg.sender];
		deposited[msg.sender] = 0;
		msg.sender.transfer(depositedValue);
		emit Refunded(msg.sender, depositedValue);
	}

	function burnAfterICO() public afterDeadline
	{
		require(!burned);

		address burner = msg.sender;
		totalSupply = totalSupply.sub(balances[this]);
		balances[this] = balances[this].sub(balances[this]);
		burned = true;
		emit Burned(burner, balances[this]);
	}

	// Need discuss with Zorayr
	function transferTokensFromContract(address _to, uint256 _value) public onlyOwner
	{
		ICO.tokens = ICO.tokens.sub(_value);
		balances[this] = balances[this].sub(_value);
		_transfer(this, _to, _value);
	}
}

contract UnitedToken is UnitedCrowdsale
{
	using SafeMath for uint;

	
	 address public companyWallet = 0x5be6A30eAe2Fb747f77e6012Df05F19e94a45e9b;
	address public internalExchangeWallet = 0x06F02d7Dfa8e254220E004af14407be1415C0c42;
	 address public bountyWallet = 0x7B710162A32dF5D9Dc817f00C5243B53C178Fe11;
	
	address public adminWallet = 0x7f7c0205acE513E20cd40B5918e0794657B87b3E;
	address public developerWallet = 0x1FDAF6AB6CeFa272e07B1B9e8C2Bc1b6Bb5b5694;

	constructor() public
	{
		
		// balances[this] = (totalSupply.mul(60)).div(100);                    // Send 60% of tokens to smart contract wallet      420,000,000 UCT
		 balances[companyWallet] = (totalSupply.mul(20)).div(100);           // Send 20% of tokens to company wallet             140,000,000 UCT
		 balances[internalExchangeWallet] = (totalSupply.mul(10)).div(100);  // Send 10% of tokens to internal exchange wallet   70,000,000 UCT
		 balances[bountyWallet] = (totalSupply.mul(5)).div(100);             // Send 5%  of tokens to bounty wallet              35,000,000 UCT
		
		balances[this] = (totalSupply.mul(100)).div(100);
		balances[adminWallet] = (totalSupply.mul(30)).div(100);
		balances[developerWallet] = (totalSupply.mul(20)).div(100);
	}

	function transferEthFromContract(address _to, uint256 amount) public onlyOwner
	{
		require(softcapReached);
		_to.transfer(amount);
	}

	/**
	 * Function to manually send tokens - by admins
	 * Assumes funds have been paid to admin by other means
	 * @param beneficiary  			[address of token reciever]
	 * @param amountOfEtherValue    [Ether value of tokens to be sent]
	 */
	function manualTokenTransfer(address beneficiary, uint256 amountOfEtherValue) public onlyOwner
	{
		require(now >= ICO.startDate);
		require(now < ICOdeadLine);
		require (whitelist[beneficiary] == 1);

		if ((now > ICO.endDate) || (ICO.tokens == 0)) {
			pauseInternal();
			emit CrowdSaleFinished(crowdSaleStatus());

			revert();
		} else {
			uint256 amountOfWei = amountOfEtherValue * 1 ether;
			paymentManager(beneficiary, amountOfWei);
		}
	}

	function () public payable
	{
		require(now >= ICO.startDate);
		require(now < ICOdeadLine);
		require (whitelist[msg.sender] == 1);

		assert(msg.value >= 1 ether / 100);

		if ((now > ICO.endDate) || (ICO.tokens == 0)) {
			pauseInternal();
			emit CrowdSaleFinished(crowdSaleStatus());

			revert();
		} else {
			paymentManager(msg.sender, msg.value);
		}
	}
}
