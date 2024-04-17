## Document Properties

| Client         | FRAX FINANCE                                   |
| :------------- | :--------------------------------------------- |
| Title          | Smart Contract Audit Report                    |
| Target         | FRAXStablecoin (FRAX)                          |
| Primary Author | Travis Moore, Jason Huan, Sam Kazemian         |
| Reviewer       |[Emmanuel Chukwuma](https://github.com/ebukizy1)|
| Classification | Public                                         |

| Version | Date           | Author                                          | Description   |
| :------ | :------------- | :-----------------------------------------------| :------------ |
| 1.0     | April 14, 2024 | [Emmanuel Chukwuma](https://github.com/ebukizy1)| Final Release |

## Table of contents

  - <a href="#dsds"> 1. INTRODUCTION</a>
  - <a href="#Disclaim"> 1.1 Disclaimer</a>
  - <a href="#About"> 1.2 About Me </a>
  - <a href="#Skills"> 1.3 Skills</a>
  - <a href="#links"> 1.4 Link</a>
  - <a href="#Ppc"> 1.5  FRAXStablecoin Prediction contract</a>
  - <a href="#scope"> 1.6 Scope</a>
  - <a href="#overview"> 1.7 System Overview</a>
  - <a href="#review"> 2.0 CONTRACT REVIEW</a>
  - <a href="#fsx"> 2.1 FSX.sol</a>
  -  <a href="2.1-fraxstablecoin">2.1 FRAXStablecoin</a>
  -  <a href="2.2-fraxshares">2.2 FRAXShares</a>
  -  <a href="2.3-fraxpool">2.3 FraxPool</a>
  - <a href="#findings"> 3.0 FINDINGS</a>
  - <a href="#summary"> 3.1 Summary</a>
  - <a href="#Qanalysis"> 3.2 Qualitative Analysis</a>
  - <a href="#keyF"> 3.3 Key Findings</a>
  - <a href="#Dresult"> 4.0 DETAILED RESULT</a>
  - <a href="#dr1"> 4.1 Missing address(0) check in constructor</a>
  - <a href="#dr2"> 4.2 Function claim() reverts without a specific pointer</a>
  - <a href="#dr3"> 4.3 BetBull/ BetBear Epoch errors could be more specific</a>
  - <a href="#dr4"> 4.4 Redundant State/ code removal in BetBear/BetBull</a>
  - <a href="#dr5"> 4.5 Redundant State/ code removal in Claim() function </a>
  - <a href="#conclusion"> 5.0 CONCLUSION</a>

<h2 id="dsds">1.0 INTRODUCTION </h2>

### <h3 id="Disclaim"> 1.1 Disclaimer <h3>

The smart contract audit I carried out is based on the information provided to me and my expertise in the field. It is not a guarantee of the security or functionality of the smart contract, and it does not eliminate all potential risks. Cryptocurrency investments come with inherent risks, and I am not liable for any financial losses that may occur as a result of investing in a smart contract or any other cryptocurrency-related activities. Investors are advised to do their own research and due diligence before making any investment decisions.

### <h3 id="About"> 1.2 🚀 About Me <h3>

Hello, my name is Chukwuma Emmanuel and I am a Smart Contract Developer. Currently, I'm interning at Web3bridge where I am working to expand my knowledge and skills in the field of blockchain development.

As a developer, I am passionate about creating secure and efficient smart contracts that can help revolutionize the way we interact with technology. I am constantly learning and exploring new technologies.

### <h3 id="Skills"> 1.3 🛠 Skills <h3>

Solidity, Diamond standard implementation, Foundry, Hardhat, Wagmi, Ethers.Js library, Java, Javascript, HTML, CSS ...

### <h3 id="links"> 1.4 🔗 Links <h3>
### <h3 id="Ppc"> 1.5 FRAXStablecoin Prediction contract<h3>

Pancakeswap Prediction is a decentralized application (dApp) built on the Binance Smart Chain (BSC) that allows users to predict the possible price change of tokens(BNB and CAKE) and earn rewards for their accurate predictions. Users must connect their BSC wallet to the platform, Stake Binance Coin (BNB) or Pancake token (CAKE) to make prediction, and earn a reward in BNB or CAKE if their prediction turns out to be correct.

### <h3 id="scope"> 1.6 Scope <h3>

_(**Table: 1.6**: PancakeSwap Prediction V2 Audit Scope)_
| Files in scope            | SLOC    |
| :-------- | :------------------------
| Contracts: 1                        |
| `FRAXStablecoin.sol`                | 
|   `Fxs.sol`                         |
|   `FraxPool.sol`                    |
| |||||||||||||||||||||||||||||||||||||
| Imports: 11      
| |||||||||||||||||||||||||||||||||||||
| `Context.sol`             |  `21`   |
| `IERC20.sol`              |         |
| `ERC20Custom.sol`         |         |
| `ERC20.sol`               |         |
| `SafeMath.sol`            |         |
| `Owned.sol`               |         |
| `FXS.sol`                 |         |
| `FraxPool.sol`            |         |
| `UniswapPairOracle.sol`   |         |
| `ChainlinkETHUSDPriceConsumer.sol`  |
| `AccessControl.sol`       |         |


### <h3 id="overview">1.7 System Overview <h3>

- #### Context.sol.
This contract, is an abstract contract that provides a foundation for other contracts to build upon. It's designed to offer a more secure and flexible way to access the `msg.sender` and `msg.data` properties, which are fundamental to understanding the context of a transaction in smart contracts. In summary, the Context contract is a foundational piece for contracts that need to securely and flexibly handle transaction context, especially in scenarios involving meta-transactions or other complex transaction flows.
- #### IERC20.sol.
  - This is a contract that allows the owner to pause and unpause the contract functionality in case of emergencies or maintenance needs.
- #### ERC20Custom.sol.
  -The contract `ERC20Custom` inherits from `Context` and implements the `IERC20` interface. This means it must implement all the functions defined in the `IERC20` interface. It uses `SafeMath` for arithmetic operations to prevent overflows and underflows.
- #### ERC20.sol.
  - This is an interface for an ERC20 token, which defines the basic functions and events for a standard token, such as transfer and approval.
- #### SafeMath.sol.
  - This contract provides additional safety checks when interacting with ERC20 tokens to avoid common errors such as overflows or underflows.
- #### Owned.sol.
- This is a contract that defines a single owner who has certain privileges such as being able to transfer ownership to another address.
- #### FXS.sol.
-  Modified ERC-20 Contract representing the FXS token, which is used for staking and governance actions surrounding the FRAX stablecoin.  
-   #### FraxPool.sol.
  - This is an interface for Chainlink's price feed oracle, which provides real-time price data for various assets.
  - #### UniswapPairOracle.sol.
  - This is an interface for Chainlink's price feed oracle, which provides real-time price data for various assets.
    - #### ChainlinkETHUSDPriceConsumer.sol.
  - This is an interface for Chainlink's price feed oracle, which provides real-time price data for various assets.
    - #### AccessControl.sol.
  - This is an interface for Chainlink's price feed oracle, which provides real-time price data for various assets.

## <h2 id="review"> 2.0 CONTRACT REVIEW  <h2>
### <h3 id="fsx"> Frax.sol Content<h3>
- <a href="#stv">1. State Variables</a>
- <a href="#mod"> 1.1 Modifiers</a>
- <a href="#con"> 1.2 Functionality review</a>

## <h4 id="stv">State Variables<h4>
-   `address public FRAXStablecoinAdd`  Address of the FRAX contract.
-   `uint256 genesis_supply`            Genesis supply of FXS  100M is printed upon genesis.  
-   `uint256 public maximum_supply`     Maximum supply of FXS.
-   `uint256 public FXS_DAO_min`        Minimum FXS required to join DAO groups.
-   `address public owner_address`      Address of the contract owner.
-   `address public oracle_address`     Address of the oracle.
-   `address public timelock_address`   Address of the timelock Governance timelock address.
-   `FRAXStablecoin private FRAX`       The FRAX contract instance.
-   `bool public trackingVotes = true`  Tracking votes (only change if need to disable votes)
- `Struct and Mappings`     
    `Checkpoint Struct`: This struct is straightforward and doesn't seem to have any security issues. It's used to track the number of votes at a specific block number or the list of voting power for a given address at a given block.  
        `Mappings`: The mappings are used to store checkpoints for each account and the number of checkpoints for each account. These are standard practices in ERC20-like contracts for tracking b
        alances and allowances.
- `mapping (address => uint32) public numCheckpoints` The purpose of this mapping is to efficiently store and retrieve the number of checkpoints for each address. A checkpoint, in the context of this contract, seems to be a record of votes at a specific block number. By keeping track of the number of checkpoints for each address, the contract can easily determine how many checkpoints are associated with an address and access them when needed.

### <h4 id="mod">modifiers <h4>
- `onlyPools`: Restricts functions to only be callable by addresses identified as FRAX pools can only mint new frax.
- `onlyByOwnGov`: Restricts functions to only be callable by the owner or the governance timelock.
### <h4 id="con"> Functionality review<h4> 
- `setOracle(address new_oracle)` :
 This function allows the contract owner or the governance timelock to update the address of the oracle. in which the modifiers onlyByOwnGov ensures that only the contract owner or the governance timelock can call this function to update the sttate variable oracle_address 

- `setTimelock(address new_timelock)` :
 This function allows the contract owner or the governance timelock to update the address of the governance timelock.
in which the modifiers onlyByOwnGov ensures that only the contract owner or the governance timelock can call this function to update the sttate variable Updates timelock_address.

- `setFRAXAddress(address frax_contract_address)`
 This function allows the contract owner or the governance timelock to update the address of the FRAX stablecoin contract.
in which the modifiers onlyByOwnGov ensures that only the contract owner or the governance timelock can call this function.

- `mint(address to, uint256 amount)`
 This function allows only FRAX pools to mint new tokens to a specified address. Modifiers: onlyPools ensures that only addresses recognized as FRAX pools by the FRAX contract can call this function.

- `pool_mint(address m_address, uint256 m_amount)` :
Purpose: Similar to mint, but specifically for FRAX pools to mint new FXS tokens. If vote tracking is enabled, it updates the votes associated with the minting in which the modifiers onlyPools ensures that only addresses recognized as FRAX pools by the FRAX contract can call this function.
State Variables: Calls _mint to mint new tokens and potentially updates votes if trackingVotes is true.
Issues:
The function does not emit an event to notify external observers of the minting. Adding an event could be beneficial for tracking token minting activities.
The function assumes that trackingVotes is correctly managed and that the trackVotes function behaves as expected.


<h3 id="2.1-fraxstablecoin">2.1 FRAXStablecoin</h3>
`FRAXStablecoin` is a partially collateralized stablecoin that dynamically adjusts its collateralization ratio based on the current market conditions. The contract is designed to operate within the Frax Finance ecosystem, interacting with other contracts such as FRAXShares (FXS) and various liquidity pools.

<h3 id="2.2-fraxshares">2.2 FRAXShares</h3>
FRAXShares (FXS) is a governance token within the Frax Finance ecosystem. It is used to accrue seigniorage revenue and to stabilize FRAX by algorithmic means. The contract includes functions for minting and burning FXS based on the needs of the system, typically in response to changes in the FRAX collateral ratio.

<h3 id="2.3-fraxpool">2.3 FraxPool</h3>
FraxPool contracts are responsible for managing the collateral used to back the FRAX stablecoin. These contracts allow users to deposit and withdraw collateral in exchange for FRAX, and they also handle the calculations for collateralization ratios and ensure that the system remains fully collateralized or properly leveraged.

<h3 id="2.4-accesscontrol">2.4 AccessControl</h3>
AccessControl is a support contract that manages role-based access control for other contracts in the ecosystem. It defines roles, manages permissions, and ensures that only authorized users can perform certain actions, enhancing the security and modularity of the system.

<h2 id="3.0-detailed-contract-review">3.0 Detailed Contract Review</h2>
<h3 id="3.1-fraxstablecoin-contract-functions">3.1 FRAXStablecoin Contract Functions</h3>
refreshCollateralRatio()
Purpose:
Dynamically adjusts the collateral ratio to ensure stability of the FRAX token around its peg.

Mechanism:
Compares the current FRAX price to a target, adjusting the collateral ratio based on predefined rules to either increase or decrease dependency on physical collateral.

Security Considerations:
Relies on external price feeds (oracles); robust against manipulation by using multiple trusted sources.

mint() and burn()
Purpose:
Manage the FRAX supply, allowing users to mint new FRAX by depositing collateral and burn FRAX to reclaim collateral.

Mechanism:
Ensures that actions are compliant with the current collateral ratio, providing mechanisms for stable expansion and contraction of supply.

Security Considerations:
Includes safeguards against reentrancy attacks and checks to prevent transactional errors like underflows/overflows.

setCollateralRatio()
Purpose:
Allows governance actions to manually adjust the collateral ratio, typically used in response to significant market events or for protocol upgrades.

Mechanism:
Restricted to addresses with governance roles; transaction emits events for transparency and auditability.

Security Considerations:
Powerful function guarded by role checks, potentially secured further by timelocks or multisig wallets.

<h2 id="4.0-key-findings">4.0 Key Findings</h2>
<h3 id="4.1-summary">4.1 Summary</h3>
|--------------------------------|
| Severity	Number of Findings   |
| High	                   0     |
| Medium	               2     |
| Low	                   3     |
| Notes	                   4     |
| Total	                   9     |
| Key Issues:                    |
---------------------------------

Medium Severity: Potential for oracle manipulation; recommended multiple oracles and periodic review of oracle integrity.
Low Severity: Gas optimizations needed in minting and burning functions to reduce user costs.
Recommendations:

Introduce additional layers of security for oracle data handling.
Optimize gas consumption by refining iterative operations and minimizing state changes.
<h2 id="5.0-conclusion">5.0 Conclusion</h2>
The FRAXStablecoin contract is robust, with well-thought-out mechanisms to maintain stability and security. The contract interacts effectively with other components of the Frax Finance ecosystem, such as FRAXShares and FraxPool, to manage the economic activities around the FRAX stablecoin. Addressing the recommendations will further enhance its reliability and efficiency.

<h2 id="6.0-acknowledgements">6.0 Acknowledgements</h2>
Frax Finance Documentation
Solidity Documentation
This review provides detailed insights into the FRAXStablecoin contract and associated contracts, offering suggestions for further strengthening the contract against potential vulnerabilities. Future updates and audits are advised to adapt to evolving security practices and market conditions.
















































