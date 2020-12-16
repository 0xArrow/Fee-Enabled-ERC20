// SPDX-License-Identifier: MIT

pragma solidity 0.4.24;

library SafeMath {
    /**
    * @dev Subtracts two numbers, reverts on overflow (i.e. if subtrahend is greater than minuend).
    */
    function sub(uint256 a, uint256 b) internal pure returns (uint256) {
        require(b <= a);
        uint256 c = a - b;

        return c;
    }

    /**
    * @dev Adds two numbers, reverts on overflow.
    */
    function add(uint256 a, uint256 b) internal pure returns (uint256) {
        uint256 c = a + b;
        require(c >= a);

        return c;
    }

    /**
    * @dev Multiplies two unsigned integers, reverts on overflow.
    */
    function mul(uint256 a, uint256 b) internal pure returns (uint256) {
        // Gas optimization: this is cheaper than requiring 'a' not being zero, but the
        // benefit is lost if 'b' is also tested.
        // See: https://github.com/OpenZeppelin/openzeppelin-solidity/pull/522
        if (a == 0) {
            return 0;
        }

        uint256 c = a * b;
        require(c / a == b);

        return c;
    }

    /**
    * @dev Integer division of two unsigned integers truncating the quotient, reverts on division by zero.
    */
    function div(uint256 a, uint256 b) internal pure returns (uint256) {
        // Solidity only automatically asserts when dividing by 0
        require(b > 0);
        uint256 c = a / b;
        // assert(a == b * c + a % b); // There is no case in which this doesn't hold

        return c;
    }
}

contract EnvContract {

    /**
     * MATH
     */

    using SafeMath for uint256;

    // ERC20 BASIC DATA
    mapping(address => uint256) internal balances;
    uint256 internal totalSupply_ = 20000000000000000000000;
    string public constant name = "Envolve Token";
    string public constant symbol = "ENV";
    uint8 public constant decimals = 18;
    address deployer = 0x76B241cfc240D719aD6c8089BcaDD18AAB163034; //Deployer address

    // ERC20 DATA
    mapping(address => mapping(address => uint256)) internal allowed;

    // FEE CONTROLLER DATA
    // fee decimals is only set for informational purposes.
    // 1 feeRate = .000001 
    uint8 public constant feeDecimals = 6;

    // feeRate is measured in 100th of a basis point (parts per 1,000,000)
    // ex: a fee rate of 200 = 0.02% 
    uint256 public constant feeParts = 1000000;
    uint256 public feeRate = 20000; //2.00%
    address public feeRecipient = 0x460A2d8DfEc851225a10a77F0854090b234cE24D;
    uint256 relaseTime = 1608098400; //unix timestamp
    address owner = msg.sender;

    /**
     * EVENTS
     */

    // ERC20 BASIC EVENTS
    event Transfer(address indexed from, address indexed to, uint256 value);

    // ERC20 EVENTS
    event Approval(
        address indexed owner,
        address indexed spender,
        uint256 value
    );

    // FEE EVENTS
    event FeeCollected(address indexed from, address indexed to, uint256 value);
    event FeeRateSet(
        uint256 indexed feeRate
    );
    event FeeRecipientSet(
        address indexed FeeRecipient
    );

    /**
     * FUNCTIONALITY
     */

    constructor() public {
        balances[owner] = totalSupply_;
        emit Transfer(address(0), deployer, 20000000000000000000000);
    }

    // ERC20 BASIC FUNCTIONALITY

    function totalSupply() public view returns (uint256) {
        return totalSupply_;
    }

    function transfer(address _to, uint256 _value) public returns (bool) {
        require(_to != address(0), "cannot transfer to address zero");
        require(_value <= balances[msg.sender], "insufficient funds");

        _transfer(msg.sender, _to, _value);
        return true;
    }

    function balanceOf(address _addr) public view returns (uint256) {
        return balances[_addr];
    }

    function approve(address _spender, uint256 _value) public returns (bool) {
        allowed[msg.sender][_spender] = _value;
        emit Approval(msg.sender, _spender, _value);
        return true;
    }

    function allowance(
        address _owner,
        address _spender
    )
    public
    view
    returns (uint256)
    {
        return allowed[_owner][_spender];
    }

    function _transfer(address _from, address _to, uint256 _value) internal returns (uint256) {
        uint256 _fee;
        uint256 _principle;
        if (now >= relaseTime) {
            _fee = getFeeFor(_value);
            _principle = _value.sub(_fee);
            balances[_from] = balances[_from].sub(_value);
            balances[_to] = balances[_to].add(_principle);
            emit Transfer(_from, _to, _principle);
            emit Transfer(_from, feeRecipient, _fee);
            if (_fee > 0) {
                balances[feeRecipient] = balances[feeRecipient].add(_fee);
                emit FeeCollected(_from, feeRecipient, _fee);
            }
        } else {
            _principle = _value;
            balances[_from] = balances[_from].sub(_value);
            balances[_to] = balances[_to].add(_principle);
            emit Transfer(_from, _to, _principle);
        }
        
        return _principle;
    }

    // FEE FUNCTIONALITY

    function getFeeFor(uint256 _value) public view returns (uint256) {
        if (feeRate == 0) {
            return 0;
        }

        return _value.mul(feeRate).div(feeParts);
    }
}
