# Reputation-Based Lending Protocol Smart Contract

## Overview

The **Reputation-Based Lending Protocol** is a decentralized lending system built on the Stacks blockchain. It implements an under-collateralized lending mechanism using an on-chain reputation scoring model to determine borrower eligibility, loan amounts, collateral requirements, and interest rates. The protocol ensures fairness and trust, rewarding borrowers for responsible behavior while penalizing defaults.

---

## Features

### 1. **Reputation Scoring System**
   - **Dynamic Credit Scores**: Tracks user behavior, including loans taken, repaid, and defaults, adjusting scores based on performance.
   - **Score Range**: From `u50` (minimum) to `u100` (maximum), ensuring nuanced borrower assessments.
   - **Score Adjustments**: Increases for timely repayments and decreases for defaults.

### 2. **Under-Collateralized Loans**
   - Borrowers with higher credit scores require less collateral and receive better interest rates.
   - Loans are associated with a repayment duration, interest rate, and collateral.

### 3. **Loan Lifecycle Management**
   - **Request Loans**: Users can request loans by specifying the amount, collateral, and duration.
   - **Repay Loans**: Borrowers can repay loans partially or in full before the due date.
   - **Loan Defaults**: Admin can mark overdue loans as defaulted, impacting the borrower's credit score.

### 4. **Admin Controls**
   - Protocol owner manages contract-specific actions like marking loans as defaulted.
   - Unauthorized access is prevented with strict ownership checks.

### 5. **Efficiency and Security**
   - Collateral is securely locked in the contract and refunded upon full loan repayment.
   - Interest rates and collateral requirements dynamically adapt to the borrower's reputation.

---

## Contract Details

### Constants

| Name                      | Description                                    |
|---------------------------|------------------------------------------------|
| `CONTRACT-OWNER`          | Address of the contract owner.                 |
| `MIN-SCORE`               | Minimum credit score (`u50`).                  |
| `MAX-SCORE`               | Maximum credit score (`u100`).                 |
| `MIN-LOAN-SCORE`          | Minimum score for requesting loans (`u70`).    |
| `ERR-*`                   | Error codes for various failure conditions.    |

---

### Data Structures

#### **UserScores Map**
Tracks user-specific data for reputation scoring.

| Field           | Type        | Description                               |
|------------------|-------------|-------------------------------------------|
| `user`          | `principal` | User address.                             |
| `score`         | `uint`      | User's reputation score.                  |
| `total-borrowed`| `uint`      | Total amount borrowed.                    |
| `total-repaid`  | `uint`      | Total amount repaid.                      |
| `loans-taken`   | `uint`      | Number of loans taken.                    |
| `loans-repaid`  | `uint`      | Number of loans fully repaid.             |
| `last-update`   | `uint`      | Block height of the last score update.    |

#### **Loans Map**
Tracks individual loan details.

| Field           | Type        | Description                               |
|------------------|-------------|-------------------------------------------|
| `loan-id`       | `uint`      | Unique identifier for the loan.           |
| `borrower`      | `principal` | Address of the borrower.                  |
| `amount`        | `uint`      | Loan amount.                              |
| `collateral`    | `uint`      | Locked collateral amount.                 |
| `due-height`    | `uint`      | Block height when repayment is due.       |
| `interest-rate` | `uint`      | Interest rate as a percentage.            |
| `is-active`     | `bool`      | Whether the loan is active.               |
| `is-defaulted`  | `bool`      | Whether the loan has defaulted.           |
| `repaid-amount` | `uint`      | Amount repaid so far.                     |

#### **UserLoans Map**
Tracks active loans for a user.

| Field           | Type        | Description                               |
|------------------|-------------|-------------------------------------------|
| `user`          | `principal` | User address.                             |
| `active-loans`  | `list`      | List of active loan IDs (up to 20).       |

---

## Public Functions

### **initialize-score**
Initializes the user's credit score to the minimum (`u50`).  
**Usage**: Call this function before interacting with the lending protocol.  
**Access**: Open to all users.  

---

### **request-loan**
Allows users to request a loan.  
**Parameters**:
- `amount` (`uint`): The desired loan amount.
- `collateral` (`uint`): The collateral provided.
- `duration` (`uint`): Loan repayment duration in blocks.

**Validations**:
- User must have a credit score ≥ `MIN-LOAN-SCORE`.
- Collateral must meet the calculated requirement.
- Users can have up to 5 active loans.  

---

### **repay-loan**
Repays a loan partially or in full.  
**Parameters**:
- `loan-id` (`uint`): ID of the loan.
- `amount` (`uint`): Repayment amount.

**Outcome**:
- Fully repaid loans unlock collateral and update credit scores.
- Loan status updates dynamically based on repayments.  

---

### **mark-loan-defaulted**
Admin function to mark loans as defaulted.  
**Parameters**:
- `loan-id` (`uint`): ID of the loan.  

**Conditions**:
- Loan must be overdue.
- Only the contract owner can call this function.  

---

## Private Functions

- **`calculate-required-collateral`**: Computes the collateral required based on the loan amount and borrower's score.
- **`calculate-interest-rate`**: Determines the interest rate based on the borrower's score.
- **`calculate-total-due`**: Calculates the total repayment amount, including interest.
- **`update-credit-score`**: Adjusts a user's score based on loan repayment or default.
- **`update-user-loans`**: Updates the user's list of active loans.

---

## Read-Only Functions

- **`get-user-score`**: Returns the user's score and credit details.
- **`get-loan`**: Retrieves loan details by `loan-id`.
- **`get-user-active-loans`**: Lists active loans for a given user.

---

## Installation and Usage

1. Deploy the contract on the Stacks blockchain using Clarinet or another tool.
2. Call `initialize-score` to register your account.
3. Request loans, repay them, and build your reputation over time.

---

## Future Enhancements

- Implement governance to allow community input on parameters like `MIN-LOAN-SCORE` and `MAX-SCORE`.
- Add support for dynamic interest rates based on market conditions.
- Enable staking pools for collateral to improve liquidity.

---

## License

This protocol is licensed under the MIT License. See the LICENSE file for details.