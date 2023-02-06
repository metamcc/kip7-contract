# Metamcc

Furthermore, Metamcc aims to produce Trust Data in both reality and metaverse. Personal and credit data will be shown in the two worlds, the real and the virtual. In reality, Metamcc’s Reliability Measure Data of Seed Networking is evident. Metamcc Seed Networking is a gift-giving mechanism where an individual’s profile reputation is reveled through interpersonal networking making it unique and easy to differentiate from any other data in the current market.  

# Smart Contract

- ## MCCXToken
    - MCCXToken is based on KIP7 standard(https://kips.klaytn.foundation/KIPs/kip-7).
    - To prevent overflow with arithmetic operations, MCCXToken uses SafeMath library
    - MCCXToken has functionalities for token transfer and blocking token transfer handled by contract
    - Blocking token transfer that locks MCCX token of specific token owener is added to prevent unfortunate accident like hacking
    - MCCXToken defines the token standard will be used for Metamcc service
    - Name: MetamccX
    - Symbol : MCCX
    - Decimals: 18
    - update date: 2022.08.10.


- ## MCCXToken lock 지원 
MCCX Token은 특정 지갑을 lock 시키기위하여 아래와 같은 method가 구현되어 있다.

    // =================================================================================================================
    //                                         Transfer Limit, sender / receiver
    // =================================================================================================================
    bytes32 public constant LIMITED_WALLET_MANAGER_ROLE = keccak256("LIMITED_WALLET_MANAGER_ROLE");

    uint8 public constant LIMITED_NONE = 0;
    uint8 public constant LIMITED_SENDER = 1;
    uint8 public constant LIMITED_RECEIVER = 2;
    uint8 public constant LIMITED_ALL = 3;

    mapping(address => bool) public limitedSenderWallets;
    mapping(address => bool) public limitedReceiverWallets; 

    /**
     * @dev Add address to limitedWallets by owner
     * @param _wallet: limitation target
     * @param _targetStatus: limitation level
     * @param _registration: true / false
     */
    function setLimitedWalletAddress(address _wallet, uint8 _targetStatus, bool _registration) public onlyRole(LIMITED_WALLET_MANAGER_ROLE) returns (bool) {
        require(LIMITED_SENDER <= _targetStatus && LIMITED_ALL >= _targetStatus);
        if (_targetStatus >= LIMITED_RECEIVER) {
            limitedReceiverWallets[_wallet] = _registration;
            _targetStatus -= LIMITED_RECEIVER;
        }
        if (_targetStatus == LIMITED_SENDER) {
            limitedSenderWallets[_wallet] = _registration;
        }
        return true;
    }

    function isLimitedWalletAddress(address _wallet) public view returns(uint8) {
        uint8 targetStatus = LIMITED_NONE;
        if (limitedSenderWallets[_wallet]) {
            targetStatus += LIMITED_SENDER;
        }
        if (limitedReceiverWallets[_wallet]) {
            targetStatus += LIMITED_RECEIVER;
        }
        return targetStatus;
    }


    /**
     * @dev Check if `account` has the assigned Pauser role via {AccessControl-hasRole}
     */
    function isLimitedWalletManager(address account) public view returns (bool) {
        return hasRole(LIMITED_WALLET_MANAGER_ROLE, account);
    }

    /**
     * @dev See {IKIP7Pausable-addPauser}
     *
     * Emits a {RoleGranted} event
     *
     * Requirements:
     *
     * - caller must have the {AccessControl-DEFAULT_ADMIN_ROLE}
     */
    function addLimitedWalletManager(address account) public onlyRole(DEFAULT_ADMIN_ROLE) {
        grantRole(LIMITED_WALLET_MANAGER_ROLE, account);
    }

    /**
     * @dev Renounce the Pauser role of the caller via {AccessControl-renounceRole}
     *
     * Emits a {RoleRevoked} event
     */
    function renounceLimitedWalletManager() public {
        renounceRole(LIMITED_WALLET_MANAGER_ROLE, msg.sender);
    }

    //============================

    특정 지갑의 수신, 전송을 lock 시킬수 있는 함수는 관리자 계정이 LIMITED_WALLET_MANAGER_ROLE 을 부여한 계정으로 만 설정 가능하며, 
    MCCX 시스템 토큰에 대한 해킹 방지등의 용도로 사용되고 있다.


- ## MCCXToken Timelock 지원
일반적으로 token contract에서는 timelock 함수등을 지원하여 투명한 토큰 배포계획등을 지원하는데, MCCX는 이러한 지원을 굿모닝 서비스를 통해 일부 필요한 토큰에 대한 timelock을 구현하여 지원한다.
굿모닝은 MCCX를 지원하는 자체 서비스 지갑으로서의 역활 뿐만아니라 굿모닝을 통한 MCCX토큰의 서비스 앱이다.
