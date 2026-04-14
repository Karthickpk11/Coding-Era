## Domain knowledge of Financial Clearing and Payment Systems and ISO20022

### 1. Financial Clearing & Payment Systems

### What is a Payment System?

A **payment system** is the infrastructure that allows money to move from a payer to a receiver. It includes banks, rules, technology, and processes.

### Key Components

* **Payer & Payee**
* **Payment Instruments** (cash, cards, digital transfers)
* **Banks / Financial Institutions**
* **Payment Networks**
* **Settlement Systems**

### What is Clearing?

**Clearing** is the process of:

* Transmitting transaction details
* Verifying information
* Calculating obligations between banks

👉 Example:
If Bank A owes Bank B ₹1000 and Bank B owes Bank A ₹600 → clearing nets it to ₹400 payable by Bank A.

### What is Settlement?

**Settlement** is the **actual transfer of funds** between banks.

* Happens after clearing
* Final and irrevocable

### Types of Settlement Systems

### 1. Gross Settlement

* Transactions processed **individually**
* No netting
* Faster but requires more liquidity

👉 Example: RTGS

* Real-time
* High-value payments

### 2. Net Settlement

* Transactions are **batched and netted**
* Less liquidity needed

👉 Example: NEFT

* Processes in batches
* Used for retail payments

### 3. Real-Time Retail Payments

* Instant settlement for individuals

👉 Example: UPI

* 24/7 instant payments
* Widely used in India

### Key Participants in Payment Systems

* Central Bank (e.g., Reserve Bank of India)
* Commercial Banks
* Payment Service Providers
* Clearing Houses

### Clearing Houses

A **clearing house** acts as an intermediary:

* Matches transactions
* Calculates net obligations
* Reduces risk

### Risks in Payment Systems

* **Credit Risk** – one party defaults
* **Liquidity Risk** – delay in payment
* **Operational Risk** – system failures
* **Systemic Risk** – failure spreads across system

---

### 2. ISO 20022 Standard

### What is ISO 20022?

ISO 20022 is a **global standard for electronic data interchange between financial institutions**.

It defines:

* Message formats
* Data structure
* Communication rules

### Why ISO 20022 Was Introduced

Older systems (like SWIFT MT messages) had:

* Limited data
* Inconsistent formats
* Poor interoperability

ISO 20022 solves this by:

* Standardizing financial messages globally
* Enabling richer, structured data

### Key Features

### 1. Rich Data Structure

* Uses XML-based format
* Includes detailed payment information:

  * Sender/receiver details
  * Purpose of payment
  * Regulatory data

### 2. Interoperability

* Works across countries and systems
* Supports cross-border payments

### 3. Flexibility

* Can be adapted for:

  * Payments
  * Securities
  * Trade finance
  * Cards

### Message Types (Examples)

* **pacs** → Payments clearing & settlement
* **pain** → Payment initiation
* **camt** → Cash management

👉 Example:

* **pacs.008** → Customer credit transfer
* **camt.053** → Bank statement

### Real-World Usage

### Global Systems Using ISO 20022

* SWIFT migration to ISO 20022
* European TARGET2 system
* Cross-border payment networks

### India Adoption

India is gradually aligning systems like:

* RTGS
* NEFT

### Benefits of ISO 20022

### 1. Better Data Quality

* Structured and standardized

### 2. Improved Compliance

* Helps with AML/KYC checks

### 3. Faster Processing

* Automation reduces manual intervention

### 4. Enhanced Transparency

* End-to-end payment tracking

### ISO 20022 vs Old SWIFT MT

| Feature     | SWIFT MT   | ISO 20022       |
| ----------- | ---------- | --------------- |
| Format      | Text-based | XML structured  |
| Data        | Limited    | Rich & detailed |
| Flexibility | Low        | High            |
| Automation  | Limited    | Advanced        |

---

### 3. How Clearing Systems and ISO 20022 Work Together

### Flow:

1. Customer initiates payment (pain message)
2. Bank processes and sends to clearing system
3. Clearing system nets/validates (pacs message)
4. Settlement occurs in RTGS or equivalent
5. Confirmation sent (camt message)

---

### 4. Simple Analogy

Think of it like:

* **Payment System** = Road network
* **Clearing** = Traffic control
* **Settlement** = Final delivery
* **ISO 20022** = Common language/signboards everyone understands

---

### 5. Key Takeaways

* Clearing = **calculation & validation**
* Settlement = **actual money transfer**
* Payment systems = **infrastructure**
* ISO 20022 = **global messaging standard**
* Future of banking = **data-rich, real-time, interoperable payments**

---

# 💳 Card-Based Money Transfer Flow (Visa / Mastercard)

```id="cardflow1"
[ Sender (Card Holder) ]
        │
        ▼
[ Initiates Payment ]
 (Swipe / Online / App)
        │
        ▼
[ Merchant / Payment Gateway ]
        │
        ▼
[ Acquiring Bank ]
 (Merchant's Bank)
        │
        ▼
[ Card Network ]
 (Visa / Mastercard)
        │
        ▼
[ Issuing Bank ]
 (Customer's Bank)
        │
        ▼
[ Authorization Response ]
 (Approved / Declined)
        │
        ▼
[ Merchant Confirmation ]
        │
        ▼
------ Clearing Phase ------
        │
        ▼
[ Card Network ]
 (Calculates obligations)
        │
        ▼
------ Settlement Phase ------
        │
        ▼
[ Settlement Bank / Central System ]
        │
        ▼
[ Acquiring Bank ]
        │
        ▼
[ Merchant Receives Funds ]
```

---

### 🔄 Simplified End-to-End Flow

```id="cardflow2"
You (Card) 
   → Merchant 
      → Acquirer Bank 
         → Visa/Mastercard Network 
            → Your Bank (Issuer)
               → Approval
            ←
         ←
      ←
   ←

(Clearing & Settlement Happens Later)
```

---

### ⚙️ Step-by-Step Explanation

### 1. Payment Initiation

* You use your card (debit/credit)
* Happens at POS machine or online

---

### 2. Authorization

* Merchant sends request to **Acquiring Bank**
* Routed via Visa / Mastercard
* **Issuing Bank** checks:

  * Balance / credit limit
  * Fraud checks
* Approves or declines

---

### 3. Clearing

* Happens in batches later
* Networks calculate:

  * Who owes whom
  * Fees (interchange, network fees)

---

### 4. Settlement

* Actual money moves:

  * Issuer → Acquirer
* Merchant receives funds (T+1 or T+2 days)

---

### 🧾 With ISO 20022 Messaging Layer

```id="cardflow3"
[ Merchant System ]
      │
      ▼
(Authorization Message - ISO format)
      │
      ▼
[ Acquirer Bank ]
      │
      ▼
[ Visa / Mastercard Network ]
      │
      ▼
[ Issuer Bank ]
      │
      ▼
(Response Message)
      │
      ▼
------ Clearing ------
      │
      ▼
(pacs messages)
      │
      ▼
------ Settlement ------
      │
      ▼
(camt messages)
```

👉 Uses ISO 20022 for:

* Structured transaction data
* Faster reconciliation
* Better fraud monitoring

---

### 🧠 Key Concepts

* **Issuer Bank** → Your bank
* **Acquirer Bank** → Merchant’s bank
* **Card Network** → Visa / Mastercard
* **Authorization** → Instant approval
* **Clearing** → Batch processing
* **Settlement** → Actual fund transfer

---

### ⚡ Quick Comparison

| Feature        | UPI       | Card (Visa/Mastercard)                    |
| -------------- | --------- | ----------------------------------------- |
| Speed          | Instant   | Authorization instant, settlement delayed |
| Intermediaries | Few       | Many                                      |
| Cost           | Low       | Higher fees                               |
| Settlement     | Real-time | T+1 / T+2                                 |
