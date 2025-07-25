# KYC-portal

## Quick start guide

Copy `.env.example` to `.env` and fill with values:

| Variable name                        | Description                                                          |
| ------------------------------------ | -------------------------------------------------------------------- |
| NEXT_PUBLIC_CHAIN_ID                 | Chain ID of chain that will be used for checking passport data.      |
| NEXT_PUBLIC_DAYS_TO_EXPIRE           | How long is the signature valid                                      |
| NEXT_PUBLIC_BACKEND_API_URL          | URL to allocator.tech API                                            |
| NEXT_PUBLIC_DECODER_CONTRACT_ADDRESS | Gitcoin Passport decoder contract address                            |
| NEXT_PUBLIC_RPC_URL                  | RPC URL for chain that will be used for checking passport data.      |
| NEXT_PUBLIC_EXPLORER_URL             | Explorer URL for chain that will be used for checking passport data. |
| NEXT_PUBLIC_CHAIN_NAME               | Name of chain that will be used for checking passport data.          |
| NEXT_PUBLIC_NATIVE_CURRENCY          | Currency chain that will be used for checking passport data.         |
| NEXT_PUBLIC_SYMBOL                   | Symbol of the native currency                                        |
| NEXT_PUBLIC_DECIMALS                 | Decimals of the native currency                                      |
| NEXT_PUBLIC_SCORE_THRESHOLD          | Minimal passport score required                                      |

```
npm ci
npm run start
```

Open [http://localhost:3000](http://localhost:3000)

---

# Developer Documentation

## Overview

This project is a Next.js-based portal for FilPlus KYC, integrating with Gitcoin Passport to verify user identity and eligibility. The portal guides users through connecting their wallet, obtaining and validating a Gitcoin Passport, and submitting their KYC application.

---

## Key Features

- **Wallet Connection:** Users connect their Ethereum wallet to interact with the portal.
- **Gitcoin Passport Integration:** Users must obtain a Gitcoin Passport and meet a minimum score threshold to proceed.
- **Onchain Verification:** The portal checks the user's passport score onchain using a smart contract.
- **KYC Submission:** Once the score is validated, users can submit their KYC application, which is cryptographically signed and sent to a backend endpoint.

---

## Gitcoin Passport Integration

### User Flow

1. **Instructions & Guidance:**  
   The UI (see `src/components/ui/mainContent.tsx`) provides step-by-step instructions for users to:
   - Obtain a Gitcoin Passport.
   - Achieve the required score.
   - Bring their passport onchain (to the Optimism network).
   - Connect their wallet and confirm ownership.

2. **Score Validation:**  
   - The `ValidatePassportScore` component (`src/components/validatePassportScore.tsx`) uses the `useGetScore` hook to fetch the user's passport score from the blockchain.
   - The score is retrieved by calling the `getScore` function on the configured smart contract (see `src/blockchain/abi.ts`).
   - The score is displayed to the user, and the parent component is notified via a callback.

3. **Score Fetching Logic:**  
   - The `useGetScore` hook (`src/lib/hooks/getScore.ts`) uses the `wagmi` library to read the `getScore` function from the smart contract.
   - The contract address and chain ID are configured via environment variables.
   - Loading state is managed to provide user feedback.

4. **KYC Submission:**  
   - Once the user's score meets the threshold, the `KycApproval` component (`src/components/ui/kycApproval.tsx`) is rendered.
   - When the user clicks "Share and submit passport," a typed data signature is generated using their wallet.
   - The signed message and signature are sent to the backend for KYC processing.

5. **Main Page Orchestration:**  
   - The main page (`src/app/(routes)/page.tsx`) orchestrates the flow:
     - Renders instructions and score validation.
     - Shows the KYC submission button only if the score is sufficient.
     - Handles modal feedback for errors and status.

---

## Key Files and Components

- **`src/app/(routes)/page.tsx`**  
  Main entry point for the user flow. Handles wallet connection, score validation, and KYC submission.

- **`src/components/ui/mainContent.tsx`**  
  Displays instructions and integrates the passport score validation component.

- **`src/components/validatePassportScore.tsx`**  
  Fetches and displays the user's Gitcoin Passport score.

- **`src/lib/hooks/getScore.ts`**  
  React hook for reading the passport score from the blockchain.

- **`src/blockchain/abi.ts`**  
  Contains the ABI for the smart contract, including the `getScore` and other relevant functions.

- **`src/components/ui/kycApproval.tsx`**  
  Handles the KYC submission process, including signing and sending the passport data.

---

## Environment Variables

- `NEXT_PUBLIC_DECODER_CONTRACT_ADDRESS`: The address of the smart contract used to fetch passport scores.
- `NEXT_PUBLIC_SCORE_THRESHOLD`: The minimum score required to proceed.
- `NEXT_PUBLIC_BACKEND_API_URL`: The backend endpoint for KYC submission.

---

## Smart Contract Integration

- The contract exposes functions such as `getScore(address)`, `getPassport(address)`, and `isHuman(address)`.
- The portal interacts with these functions using the `wagmi` library and the provided ABI.

---

## Extending or Modifying the Integration

- To change the score threshold, update the `NEXT_PUBLIC_SCORE_THRESHOLD` environment variable.
- To support additional chains or contracts, update the contract address and chain configuration.
- To modify the KYC submission logic, edit `src/components/ui/kycApproval.tsx` and the backend endpoint.

---

## Additional Notes

- The UI uses Tailwind CSS for styling.
- Wallet connection is handled via RainbowKit and wagmi.
- The codebase is modular, with hooks and providers for state management and API calls.