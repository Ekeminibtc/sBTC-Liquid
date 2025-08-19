
## sBTC-Liquid - Overview

This contract implements a **hybrid derivatives protocol** on the Stacks blockchain, combining **options trading** and **perpetual positions**.

It allows users to:

* Create, buy, and exercise **call/put options**.
* Open and manage **perpetual leveraged positions** (long or short).
* Track balances, funding rates, and liquidation conditions.

The contract is designed as a **foundation layer** for decentralized derivatives, with room for future upgrades such as price oracles, automated funding payments, and insurance mechanisms.

---

## ✨ Features

### Options Module

* **Create Option:** Any user can create an option specifying strike price, expiry block, amount, premium, and type (call/put).
* **Buy Option:** A buyer pays the premium to the option creator.
* **Exercise Option:** If the market conditions favor the buyer before expiry, the option can be exercised to transfer STX between parties.
* **Expiry Validation:** Prevents exercising expired options.

### Perpetual Positions Module (Scaffolded)

* Defines leverage caps, liquidation thresholds, and funding rate logic.
* Positions track entry price, margin, size, and liquidation status.
* Provides room for funding rate adjustments and liquidation execution.

---

## 🔑 Constants

* **Contract Logic**

  * `MAX_LEVERAGE = u10` → Users can trade with up to 10x leverage.
  * `LIQUIDATION_THRESHOLD = u80` → Positions get liquidated if margin falls below 80% requirement.
  * `FUNDING_RATE_PRECISION = u1000000` → Precision factor for funding rate calculations.

* **Errors**

  * `ERR_NOT_AUTHORIZED` → Unauthorized caller.
  * `ERR_INVALID_AMOUNT` → Invalid STX/token amounts.
  * `ERR_OPTION_NOT_FOUND` → Invalid option ID.
  * `ERR_OPTION_EXPIRED` → Attempt to interact with an expired option.
  * `ERR_INSUFFICIENT_BALANCE` → Not enough STX to cover transfer or premium.
  * `ERR_POSITION_NOT_FOUND` → Invalid position ID.
  * `ERR_INSUFFICIENT_MARGIN` → Position does not meet margin requirements.
  * `ERR_POSITION_LIQUIDATED` → Position is already liquidated.
  * `ERR_INVALID_LEVERAGE` → Leverage exceeds `MAX_LEVERAGE`.

---

## 📂 Data Structures

* **Options Map (`options`)**

  ```
  {
    creator: principal,
    strike-price: uint,
    expiry: uint,
    amount: uint,
    premium: uint,
    exercised: bool,
    option-type: (string-ascii 4) ;; "call" or "put"
  }
  ```

* **User Balances (`user-balances`)**

  * Tracks STX balances for each user.

* **Positions Map (`positions`)**

  ```
  {
    trader: principal,
    size: uint,
    entry-price: uint,
    margin: uint,
    leverage: uint,
    is-long: bool,
    liquidated: bool,
    last-funding-payment: uint
  }
  ```

* **User Positions (`user-positions`)**

  * List of position IDs associated with a user (max 100).

---

## ⚙️ Public Functions

### Options

* `(create-option strike expiry amount premium option-type)`
  Creates a new call/put option. Returns option ID.

* `(buy-option option-id)`
  Transfers the premium from buyer to creator.

* `(exercise-option option-id)`
  Executes the option and transfers STX depending on type.

### Read-Only Helpers

* `(get-option option-id)` → Returns option details.
* `(get-next-option-id)` → Returns the next available option ID.
* `(is-option-expired option-id)` → Boolean flag if expired.

---

## 🚀 Future Improvements

* **Price Oracle Integration:** Replace `current-stx-price` with a trusted on-chain oracle feed.
* **Funding Rate Mechanics:** Implement periodic funding payments between long and short perpetuals.
* **Liquidation Engine:** Automate liquidation when margin falls below threshold.
* **Fee Mechanism:** Introduce protocol-level fees to fund insurance and development.
* **Collateral Flexibility:** Allow collateral in tokens beyond STX.

---

## 🛠️ Development & Testing

1. Deploy the contract on **Stacks testnet**.
2. Use Clarinet or Hiro Wallet for simulation.
3. Run scenarios:

   * Create → Buy → Exercise option lifecycle.
   * Attempt to buy expired options (should fail).
   * Test insufficient balance scenarios.
   * Scaffold perpetual position management.

---

## 📜 License

MIT License. Free to use and extend for derivative protocol development.

---