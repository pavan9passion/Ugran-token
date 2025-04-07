# Ugran-token
Smart contract for Ugran( UGN) bep20 token
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract Ugran is ERC20, Ownable {
    uint256 public constant TOTAL_SUPPLY = 90_000_000 * 10 ** 18;
    uint256 public constant MINTABLE_SUPPLY = (TOTAL_SUPPLY * 25) / 100;
    uint256 public constant MINT_START_DELAY = 15 * 30 days;

    address public creator;
    uint256 public mintStartTime;
    uint256 public mintedAmount;

    constructor(
        address _creator,
        address _liquidityWallet,
        address _marketingWallet,
        address _airdropWallet
    ) ERC20("Ugran", "UGN") {
        creator = _creator;
        mintStartTime = block.timestamp + MINT_START_DELAY;

        // Initial Distribution
        _mint(_creator, (TOTAL_SUPPLY * 15) / 100); // 15% to Creator immediately
        _mint(_liquidityWallet, (TOTAL_SUPPLY * 30) / 100); // 30% to Liquidity
        _mint(_marketingWallet, (TOTAL_SUPPLY * 20) / 100); // 20% to Marketing
        _mint(_airdropWallet, (TOTAL_SUPPLY * 10) / 100); // 10% to Airdrop
    }

    function mint(uint256 amount) external {
        require(msg.sender == creator, "Only creator can mint");
        require(block.timestamp >= mintStartTime, "Minting not started yet");

        uint256 maxMonthly = (MINTABLE_SUPPLY * 5) / 100; // Max 5%
        uint256 minMonthly = (MINTABLE_SUPPLY * 25) / 1000; // Min 2.5%

        require(amount >= minMonthly && amount <= maxMonthly, "Invalid mint amount");
        require(mintedAmount + amount <= MINTABLE_SUPPLY, "Exceeds mintable supply");

        mintedAmount += amount;
        _mint(creator, amount);
    }
}
