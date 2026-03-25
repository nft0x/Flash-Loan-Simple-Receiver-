# Flash-Loan-Simple-Receiver-
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v5.0.0/contracts/interfaces/IERC3156FlashLender.sol";
import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v5.0.0/contracts/security/ReentrancyGuard.sol";

contract FlashLoanReceiver is ReentrancyGuard {
    IERC3156FlashLender public lender;

    constructor(address _lender) {
        lender = IERC3156FlashLender(_lender);
    }

    function executeFlashLoan(uint256 amount) external nonReentrant {
        bytes memory data = "";
        lender.flashLoan(address(this), address(lender), amount, data); // demo
    }

    function onFlashLoan(
        address initiator,
        address token,
        uint256 amount,
        uint256 fee,
        bytes calldata data
    ) external returns (bytes32) {
        require(initiator == address(this), "Untrusted loan initiator");
        // Thực hiện logic arbitrage hoặc liquidation ở đây
        // Trả lại loan + fee
        IERC20(token).approve(address(lender), amount + fee);
        return keccak256("ERC3156FlashBorrower.onFlashLoan");
    }
}
