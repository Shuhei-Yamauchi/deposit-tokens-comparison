---
layout: default
title: "Comparison and Implementation Feasibility of Backed Deposit Tokens and Native Deposit Tokens"
---

# 1. Introduction (Background and Purpose)

In recent years, global attention has turned to tokenizing bank deposits using blockchain technology. Often referred to as “tokenized deposits” or “deposit tokens,” these digital representations of bank liabilities combine the stability of conventional deposits with the programmability of crypto-asset technology. Traditionally, crypto-assets such as Bitcoin have shown high volatility and limited viability as day-to-day payment instruments. In contrast, deposit tokens hold the potential to become a stable digital payment instrument, thanks to their peg to fiat currency and issuance by regulated financial institutions.

There are two main approaches to creating tokenized bank deposits:

- **Backed Deposit Tokens**
- **Native Deposit Tokens**

This paper provides a systematic comparison of these two models in terms of technical architecture, issuance/redemption mechanisms, node management, external integration, KYC (Know Your Customer) processes, and key use cases. We will also clarify each model’s advantages and challenges, offering insight for the design and adoption of next-generation financial infrastructure.

The structure of this paper is as follows. Section 2 compares the technical architectures of Backed Deposit Tokens and Native Deposit Tokens. Section 3 addresses how issuance (minting) and redemption (burning/converting tokens back to deposits) are implemented, including the role of smart contracts. Section 4 examines node management, external system interfaces, and KYC/AML compliance. Section 5 lists the main advantages and challenges of each model. Section 6 focuses on typical use cases—domestic remittances, business-to-business (B2B) settlements, and cross-border payments—and analyzes the suitability of each token type. Section 7 provides discussion on implementation strategy, potential hybrid approaches, and governance. Section 8 concludes.

# 2. Technical Architecture Comparison

When tokenizing bank deposits, the choice between Backed Deposit Tokens and Native Deposit Tokens leads to fundamentally different ledger structures. According to the European Banking Authority (EBA), deposit tokens can be categorized as either “native” (i.e., recorded directly on a distributed ledger) or “non-native” (where the ledger representation references an off-chain asset). This distinction broadly aligns with our definitions here:

- **Backed Deposit Tokens:** The tokens are backed by existing bank deposits in an off-chain account, operating via a dual-layer model that combines a conventional bank ledger with a blockchain ledger.
- **Native Deposit Tokens:** Bank deposit records themselves reside on a DLT (distributed ledger technology) system, such that the DLT is effectively the primary record of deposit balances.

## 2.1 Backed Deposit Tokens (Dual-Layer Structure)

In Backed models, the bank’s traditional core banking ledger coexists with a blockchain ledger (a secondary ledger or sub-ledger). For every token balance on the blockchain, there is a corresponding deposit “backing” it in an off-chain bank account. As a result, token transfers on-chain must be reconciled with the bank’s off-chain account records, ensuring the backing deposit remains in sync with the on-chain tokens.

This arrangement can be deployed on a permissioned (private) blockchain managed by the issuing bank(s), or sometimes on a public blockchain (like Ethereum). In either case, the bank needs to ensure that tokens cannot exceed the total value of deposits (the backing) and that on-chain/off-chain reconciliation is reliable. The bank’s existing core systems remain intact, and the blockchain acts more like an auxiliary ledger.

## 2.2 Native Deposit Tokens (Single-Layer Structure)

Native Deposit Tokens, by contrast, replace or integrate the conventional deposit ledger with a blockchain ledger that becomes the primary record of deposits. Here, the token itself constitutes a direct claim on the bank. Once a user’s on-chain balance changes, that update is legally recognized as the official deposit balance. There is no separate off-chain account with a corresponding deposit. Rather, the DLT is the main ledger, and deposit accounts effectively exist as addresses on the blockchain.

This model removes the need for reconciliation with an off-chain core banking system—everything is recorded in real time on the single, unified ledger. However, migrating the bank’s core system to a blockchain-based environment can be a major undertaking, requiring high levels of reliability, availability, and strong governance. In multi-bank scenarios, multiple institutions might collaborate on a consortium ledger where each bank issues its own Native Deposit Tokens, or where a single shared token represents collectively managed deposits.

# 3. Issuance, Redemption, and Smart Contracts

Both Backed and Native Deposit Tokens maintain a strict 1:1 link to actual bank deposit value. Yet their processes for minting (issuance) and burning/redemption differ. In both models, smart contracts play a key role in enforcing the logic of issuance, redemption, and compliance.

## 3.1 Backed Deposit Token Issuance and Redemption

**Issuance:** When a customer deposits fiat currency (e.g., by transferring 1 million yen to their bank), the bank “locks” that deposit as the backing asset and mints a corresponding amount of deposit tokens on the blockchain. The bank’s smart contract enforces a maximum issuance limit tied to the total locked deposits, ensuring a 1:1 backing. Off-chain and on-chain balances must be reconciled periodically (or in real time) via APIs or batch processes between the bank’s core systems and the blockchain.

**Redemption:** If the user wants to convert tokens back into fiat, the bank’s smart contract burns the tokens and credits the user’s off-chain bank account with an equivalent deposit. This two-way process (minting and burning) keeps the total supply of tokens fully collateralized. The contract logs all mint and burn events, ensuring that on-chain circulating tokens never exceed the actual deposit pool locked off-chain.

## 3.2 Native Deposit Token Issuance and Redemption

**Issuance:** In the Native model, depositing funds into a bank is essentially the same as minting new tokens on-chain. Suppose a customer deposits 1 million yen at a bank branch or via a wire transfer. The bank then reflects this deposit directly on the blockchain ledger by increasing the token balance in that customer’s on-chain account. Formally, the bank is issuing tokens that represent its deposit liability to the customer, but the ledger of record is the blockchain itself.

**Redemption:** When a customer withdraws funds, the bank’s smart contract or DLT infrastructure reduces (burns) the corresponding token amount on-chain and hands over fiat cash or processes another form of settlement. As soon as the on-chain balance is updated, it becomes the official deposit record. There is no separate deposit backing required because the “deposit” is the token itself.

In both models, smart contracts may provide advanced functionality like programmable payments, automatic escrow, or atomic swaps (e.g., Delivery-vs-Payment with other asset tokens). With the Native model, these contract-based features can be even more seamlessly integrated, since all deposit transactions take place on-chain. However, the governance risk is higher: a malfunction or security flaw in the DLT or smart contracts could directly affect the bank’s core deposit records.

# 4. Node Management, External Interfaces, and KYC

## 4.1 Node Management and Governance

On a permissioned blockchain, only pre-approved participants operate nodes. This often involves a consortium of banks and related entities (potentially including a central bank or settlement institution). They collectively manage software updates, network governance, and transaction validation. With Backed Deposit Tokens, the issuing bank typically maintains node(s) to handle minting, burning, and transactions. If multiple banks issue tokens on the same network, each institution may have its own smart contract authority for issuance and redemption.

For Native Deposit Tokens, especially across multiple banks, the DLT network is effectively the shared ledger of record for all participants’ deposit liabilities. A robust consensus algorithm (e.g., BFT-based) ensures finality, and each bank must run nodes with high reliability. Any outage or malfunction at a single node can affect the entire deposit ledger, making governance and failover processes critically important.

## 4.2 External System Integration

For Backed Deposit Tokens, real-time integration with the bank’s existing core banking systems is essential. Whenever a token transaction is initiated, the bank must update the corresponding off-chain deposit account or hold the deposit as collateral. Banks typically implement APIs or intermediary servers to coordinate on-chain and off-chain events. Complex orchestration is required to keep both ledgers in sync, particularly in multi-bank transactions involving a chain of mint-burn operations.

For Native Deposit Tokens, the blockchain itself becomes the bank’s core deposit ledger. This eliminates the need for off-chain reconciliation but does require integrating any ancillary systems (e.g., accounting, risk management, regulatory reporting) with the DLT network. Banks might still rely on external settlements (like a central bank RTGS) for final netting or liquidity management. When multiple banks adopt a Native approach, they may form a shared consortium ledger where each institution’s deposits are recorded, potentially connecting multiple blockchains or bridging to other networks for interoperability.

## 4.3 KYC and Account Management

Both models are typically deployed on permissioned blockchains. Users (token holders) undergo KYC verification by a bank or trusted authority, and accounts (addresses) are managed in an account-based system. In the Backed model, wallet addresses correspond to a bank’s customer account, restricting token transfers to approved participants (either within the same bank or across banks in the same network). In the Native model, addresses are similarly tied to verified user identities, preventing unauthorized or fully anonymous transfers.

Such controlled access aligns with standard AML/CFT requirements, as each transaction party can be identified. The bank can also implement whitelisting or blacklisting in the smart contract to prevent illicit transfers. This is a key advantage of permissioned DLT: it balances the benefits of blockchain immutability with regulatory compliance for banks.

# 5. Strengths and Challenges of Backed vs. Native Models

Below is a summary of key strengths and potential challenges for each model, along with implementation considerations.

## 5.1 Backed Deposit Tokens

**Strengths**

- **Compatibility with existing systems:** Minimal disruption to a bank’s existing core ledgers. The bank can enjoy tokenization benefits without overhauling its entire infrastructure, making it easier to align with current regulations and accounting treatments.
- **Phased implementation:** Banks can start with limited use cases and gradually expand on-chain functionality. Pilots can be done in controlled environments.
- **Clear collateral:** If a blockchain problem arises, the underlying deposits remain intact in the bank’s off-chain ledger. In extreme scenarios, the bank can revert to existing infrastructure.

**Challenges**

- **Ledger reconciliation risk:** Maintaining consistency between on-chain token balances and off-chain deposit accounts demands precise real-time synchronization. Any mismatch can cause serious errors (e.g., double counting).
- **Complex processes:** Each transaction involves behind-the-scenes reconciliation. In multi-bank transactions, multiple mint/burn events and cross-institution settlement can complicate workflows.
- **Reliance on off-chain processes:** The blockchain can settle instantly, but real money movement may be delayed if the core banking system lags. Often, the main bottleneck is off-chain reconciliation rather than on-chain throughput.

## 5.2 Native Deposit Tokens

**Strengths**

- **Efficiency and immediacy:** A single ledger ensures real-time, global balance updates and guaranteed finality. Removing redundant reconciliation steps can simplify payment workflows and reduce costs.
- **Programmability:** With the entire deposit record on the blockchain, advanced smart contract logic (e.g., conditional payments, multi-party atomic settlement) can be deployed. This significantly broadens automation potential for banking services.
- **Transparency and real-time audit:** Regulators and auditors can, in principle, access on-chain transaction histories for higher traceability and more efficient oversight. Permissioned settings preserve data confidentiality while selectively granting read permissions.

**Challenges**

- **Migration hurdles:** Replacing or integrating core banking systems with a DLT requires significant technical and operational changes. The bank must replicate its reliability and performance standards on the new platform.
- **Governance and resilience:** In a multi-bank environment, network-wide governance must define responsibilities, risk-sharing, and fallback procedures. A single participant’s failure might impact the entire ledger.
- **Regulatory alignment:** Even if the legal status of deposit tokens is consistent with deposit liabilities, issues remain around contract enforceability, system downtime, dispute resolution, and more.

Both models share the benefit of leveraging the inherent stability of bank deposits, always redeemable at a 1:1 ratio to fiat currency. Moreover, bank issuance brings stronger KYC/AML compliance and deposit insurance coverage—unlike many privately issued stablecoins. A permissioned blockchain framework can also embed regulatory requirements directly into the network’s infrastructure. In this sense, deposit tokens realize a unique fusion of digital asset flexibility and conventional bank deposit protection.

# 6. Key Use Cases, Application Scenarios, and Suitability

How do these differing features manifest in real-world applications? Below, we compare Backed and Native approaches across three primary use cases: domestic remittances, business-to-business (B2B) settlements, and cross-border payments.

## 6.1 Domestic Remittances (Individual and Corporate Transfers)

**Scenario:** Person A at Bank A wants to send money to Person B at Bank B domestically.

- **Backed Deposit Tokens:** Bank A burns A’s tokens and effectively transfers the underlying deposit to Bank B, which mints its own tokens for B. The bank-led conversion process (often called “burn and mint”) can leverage existing interbank settlement networks for final settlement (e.g., real-time gross settlement or next-day net settlement). This approach integrates well with current systems but involves complexity when bridging tokens across different banks.
- **Native Deposit Tokens:** If Banks A and B run a shared DLT platform, deposit tokens can move directly from A’s on-chain address to B’s on-chain address in real time. The ledger simultaneously reflects the necessary interbank fund movements, possibly leveraging net or gross settlement at the central bank. A single, unified ledger allows 24/7 finality even across banks, though it requires a robust consortium environment and strong governance.

## 6.2 B2B Payments (Corporate Settlements and Supply Chain Finance)

**Scenario:** Large companies or SMEs conducting high-value payments for goods/services.

- **Backed Deposit Tokens:** Corporations can hold tokenized deposits from their bank. When settling with another bank’s corporate client, the tokens must be burned and re-minted under the second bank. This can be automated with orchestration or smart contracts that ensure near-instant settlement (delivery vs. payment, or DvP). One advantage is that corporations continue to treat the tokens as offshoots of their trusted bank deposits. They benefit from safety and familiarity, though behind the scenes there may be multiple inter-bank settlement steps.
- **Native Deposit Tokens:** If many companies and banks are on the same DLT platform, B2B settlements occur as immediate changes in on-chain balances. This suits real-time treasury management (e.g., intra-group liquidity) and advanced programmable finance. Automated multi-party settlement can be implemented (e.g., auto-payment upon invoice verification). A single ledger environment greatly simplifies workflows, though it demands that all participants join the same network. This fosters efficiency but also raises consortium-level governance and standardization challenges.

## 6.3 Cross-Border Payments (International Remittances and FX Settlements)

**Scenario:** Sending funds across currencies and borders. Today’s SWIFT-based correspondent banking network is costly and slow.

- **Backed Deposit Tokens:** Each currency is represented by deposit tokens issued by a bank in the relevant jurisdiction. A user can send JPY-backed tokens to a U.S. bank, which redeems them for JPY and issues USD-backed tokens. Settlement behind the scenes might involve local real-time settlement or netting. This approach can cut out intermediary correspondent banks, lowering costs and processing times. JP Morgan’s cross-border deposit token concept, for example, aims to reduce friction by allowing direct token exchanges and near-instant settlement 24/7, with built-in compliance checks.
- **Native Deposit Tokens:** An ideal scenario might involve multiple currencies on a shared international DLT, such as an integrated ledger under discussion by the BIS or various central banks. Each bank issues native deposit tokens in its own currency, enabling real-time Payment-vs-Payment (PvP) if the network supports atomic swaps. This could eliminate time-zone delays and reduce reliance on correspondent banks. However, building a truly global DLT ecosystem requires close coordination among regulators, standard-setters, and the banking sector. The challenges—technical, legal, and governance-related—remain substantial.

# 7. Discussion: Implementation Strategy and Hybrid Deployment

From the above comparisons, it appears that Backed Deposit Tokens are easier to introduce while maintaining existing systems, whereas Native Deposit Tokens offer a more unified ledger with enhanced functionality but a higher bar for system migration and governance.

## 7.1 Phased Approach to Implementation

A staged rollout can be prudent. Banks might initially pilot Backed Deposit Tokens in limited domains (e.g., large corporate clients, specific currency corridors) to gather operational experience and see how reconciliation complexities can be managed. Over time, if the market proves the benefits of DLT-based transactions, banks may evolve toward Native Deposit Tokens, migrating larger portions of their core deposit infrastructure onto a blockchain. Consistent interoperability standards are crucial here, so that partial or transitional models (where on-chain and off-chain ledger coexist) can remain manageable.

## 7.2 Hybrid Models

Hybrid approaches combine features of both. For instance, multiple banks might each use a Native approach internally but adopt a common Backed token for interbank settlement or for bridging to external networks. Alternatively, a consortium could implement a single token for all participants, but with partial off-chain processes retained for certain regulatory or operational reasons. Another angle involves central bank digital currencies (CBDCs). A model known as RLN (Regulated Liability Network) envisions both commercial bank deposit tokens and central bank money coexisting on a unified ledger, maintaining their distinct roles (public money vs. private bank money) yet enabling instant cross-currency or cross-institution settlement.

In any scenario, developers and regulators need to address how to handle fallback or system downtime, how deposit insurance applies, how to manage the risk of a single node disruption in a multi-bank ledger, and how to standardize compliance across jurisdictions.

## 7.3 Legal and Regulatory Perspectives

Legal clarity is vital. Even if deposit tokens preserve the essence of a “bank deposit,” specific laws may treat tokenized forms differently regarding deposit insurance, consumer protections, or security definitions. For Backed tokens, questions arise around whether the token confers a direct deposit right or a separate claim on a backing entity. For Native tokens, authorities will likely scrutinize the reliability of a DLT-based core banking system and how it meets regulatory standards for safety and soundness. In Japan, for instance, new rules under the Payment Services Act permit banks to issue digital money, but a dedicated framework for deposit tokens is still evolving. Similar dialogues are unfolding in the U.S., Europe, and other financial jurisdictions.

## 7.4 Interoperability and Industry Standards

End-user convenience and ecosystem growth depend on seamless interoperability. If every bank issues its own deposit token without common standards, businesses and individuals may face fragmentation and complexity. Industry bodies or consortiums could pursue a unified protocol layer for settlement, either bridging multiple blockchains or establishing a standard token format for all banks. Eventually, the distinction between “Backed” and “Native” might recede from the user’s perspective, as both are integrated under robust, standardized networks.

# 8. Conclusion

This paper has examined two principal approaches to deposit tokenization—Backed Deposit Tokens and Native Deposit Tokens—through technical architecture, issuance/redemption processes, operational models, use-case suitability, and their respective advantages and challenges. The Backed model relies on a dual-layer design in which off-chain deposits serve as collateral, offering flexibility with existing systems but demanding ongoing reconciliation and clarity around deposit protection. The Native model places the deposit itself on a DLT ledger, delivering a powerful combination of real-time settlement and full programmability, while necessitating a major core system transformation and comprehensive governance.

Both share a common objective: merging the stability, security, and regulatory compliance of bank deposits with the functional benefits of blockchain-based digital assets. Many experts, including Oliver Wyman and J.P. Morgan, argue that deposit tokens may become a cornerstone of stable digital payments, acting as a regulated and insured means of digital settlement.

That said, serious attention must be paid to technical resilience, legal frameworks, interoperability, and industry-wide coordination. A staged approach—starting with limited Backed token use and gradually migrating to the Native model—may be most realistic. Throughout, success will hinge on establishing consistent standards, governance structures, and user-friendly integration with the broader financial system. Combined with potential interplay between commercial deposit tokens and central bank digital currencies, the advent of deposit tokens could reshape domestic and cross-border payment infrastructures by making them faster, more secure, and more programmable.

If adopted carefully, deposit tokens (whether Backed or Native) promise to unlock more efficient and versatile digital financial services—both for routine domestic payments and for complex international transactions. It is hoped that the analysis presented here will contribute to the broader discussions on how to shape next-generation financial infrastructures, ensuring safety, resilience, and innovation.

# References (Selected)

- Bank of Japan: “Initiatives for Tokenized Bank Deposits Overseas,” BOJ Review, June 2024.
- European Banking Authority: Report on Tokenised Deposits, December 2024.
- Oliver Wyman / Onyx (J.P. Morgan): Deposit Tokens: A Foundation for Stable Digital Money, February 2023.
- Swiss Bankers Association (SBA): The Deposit Token – New money for digital Switzerland, March 2023.
- JPMorgan Chase & Co.: “JPM Coin and the Onyx Platform,” Press Release, 2020.
- BIS: Project Agorá Report, 2023.
- Digital Currency Forum (Japan): Proof-of-Concept Reports and Analysis, 2022–2024.
