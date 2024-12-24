# TwentyOne - Findings Report

# Table of contents
- ## [Contest Summary](#contest-summary)
- ## [Results Summary](#results-summary)
- ## High Risk Findings
    - ### [H-01.  Missing Owner Withdrawal Functionality in the TwentyOne Contract](#H-01)
- ## Medium Risk Findings
    - ### [M-01.  Insufficient Initial Contract Balance Prevents First Player's Rewards in endGame Function](#M-01)



# <a id='contest-summary'></a>Contest Summary

### Sponsor: First Flight #29

### Dates: Nov 21st, 2024 - Nov 28th, 2024

[See more contest details here](https://codehawks.cyfrin.io/c/2024-11-twentyone)

# <a id='results-summary'></a>Results Summary

### Number of findings:
- High: 1
- Medium: 1
- Low: 0


# High Risk Findings

## <a id='H-01'></a>H-01.  Missing Owner Withdrawal Functionality in the TwentyOne Contract            



## Summary

The `TwentyOne` contract currently lacks functionality for the contract owner to withdraw collected fees. This omission prevents the owner from accessing the contract's accumulated balance, which may include unused funds or player deposits.

This could result in funds being permanently locked within the contract, especially if payouts to players do not fully deplete the balance. Such a scenario can impact the operational sustainability of the game.

## Vulnerability Details

Deploy the contract on a testnet.

* Have a player start a game by sending 1 ETH to the `startGame` function.
* Observe that the contract balance increases.
* Attempt to withdraw the balance as the contract owner (no functionality exists to perform this action).

## Impact

Locked funds reduce operational efficiency and may cause dissatisfaction for the contract owner.

* Potential financial loss for the owner if funds cannot be retrieved.

## Tools Used

manual review 

## Recommendations

```Solidity
modifier onlyOwner() {
    require(msg.sender == owner, "Caller is not the owner");
    _;
}

function withdralFunds() external onlyOwner {
    payable(owner).transfer(address(this).balance);
}

```

    
# Medium Risk Findings

## <a id='M-01'></a>M-01.  Insufficient Initial Contract Balance Prevents First Player's Rewards in endGame Function            



## Summary

The `TwentyOne` contract does not initialize with a sufficient balance to ensure that the first player can receive the reward of 2 Ether upon winning. This can lead to a scenario where the contract lacks the required Ether to fulfill the payout, causing the transaction to revert and leaving the first player without their expected winnings.

## Impact

* The contract becomes unusable for the first player due to the lack of initial funding.
* Player trust is undermined as winnings cannot be reliably paid out.
* The contract's functionality is dependent on external funding, which is not guaranteed.

## Tools Used

Manual Review

## Recommendations

**Seed the Contract with Ether on Deployment**:\
Add an Ether requirement during contract deployment to ensure sufficient funds are available for initial payouts.

```Solidity
constructor() payable {
    require(msg.value >= 10 ether, "Initial funding required");
}

```
