# FAPEX Frontend - Setup & Deployment Guide

## Overview

FAPEX is a decentralized freelance marketplace built on blockchain technology. This frontend is built with React 19, Next.js, TailwindCSS 4, and integrated with Web3 technologies (ethers.js, wagmi).

## Features

### 1. Landing Page
- Hero section with CTA buttons
- Platform statistics
- Featured opportunities
- Category browsing
- How it works section
- Footer with social links

### 2. Marketplace
- Browse available jobs
- Advanced filters (skill, budget, deadline, status)
- Search functionality
- Pagination
- Job detail cards with StatusBadge

### 3. Client Dashboard
- Posted jobs management
- Job status tracking (OPEN, ASSIGNED, IN_PROGRESS, SUBMITTED, COMPLETED, DISPUTED)
- Milestone management
- Create new job button

### 4. Freelancer Dashboard
- Available jobs browsing
- Bid submission
- Active jobs tracking
- Deliverable management
- Earnings history
- Reputation score display

### 5. Arbitrator Dashboard
- Open disputes list
- Dispute detail view
- Evidence viewing
- Voting interface
- Resolution tracking

### 6. Create Job (2-Step Flow)
- **Step 1**: Job details form (title, description, skills, budget, deadline, milestones, file upload)
- **Step 2**: Blockchain confirmation (USDC approval, escrow deposit)

### 7. Review Deliverable
- Download file from IPFS
- Hash verification on-chain
- Approve & release payment
- Raise dispute with evidence

### 8. User Profile
- User information display
- Reputation score
- Job history
- Reviews and ratings
- Leaderboard ranking
- Activity feed

## Environment Setup

### Prerequisites
- Node.js 18+ (recommended: 22.13.0)
- pnpm 10.4.1+
- MetaMask or WalletConnect compatible wallet

### Installation

```bash
# Clone repository
git clone https://github.com/thanhntm2202/fontendtest.git
cd fapex-frontend

# Install dependencies
pnpm install

# Create .env.local file
cp .env.example .env.local
```

### Environment Variables

```env
# Blockchain Configuration
VITE_CHAIN_ID=1  # Ethereum mainnet (or your target chain)
VITE_RPC_URL=https://eth-mainnet.g.alchemy.com/v2/YOUR_KEY
VITE_EXPLORER_URL=https://etherscan.io

# Smart Contract Addresses
VITE_USDC_ADDRESS=0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48
VITE_ESCROW_VAULT_ADDRESS=0x...  # Your EscrowVault contract
VITE_ARBITRATOR_ADDRESS=0x...    # Your Arbitrator contract

# IPFS Configuration
REACT_APP_PINATA_API_KEY=your_pinata_api_key
REACT_APP_PINATA_SECRET_KEY=your_pinata_secret_key

# Or use alternative IPFS provider
VITE_IPFS_GATEWAY=https://gateway.pinata.cloud
VITE_IPFS_UPLOAD_URL=https://api.pinata.cloud/pinning/pinFileToIPFS

# Analytics (Optional)
VITE_ANALYTICS_ENDPOINT=your_analytics_endpoint
VITE_ANALYTICS_WEBSITE_ID=your_website_id
```

## Development

### Start Development Server

```bash
pnpm dev
```

Server will run on `http://localhost:3000`

### Build for Production

```bash
pnpm build
pnpm start
```

### Run Tests

```bash
pnpm test
```

### Type Checking

```bash
pnpm check
```

### Format Code

```bash
pnpm format
```

## Project Structure

```
fapex-frontend/
├── client/
│   ├── public/              # Static assets
│   ├── src/
│   │   ├── components/      # Reusable UI components
│   │   ├── contexts/        # React contexts (Web3, Theme)
│   │   ├── hooks/           # Custom hooks (useWeb3Contract, useIPFSUpload)
│   │   ├── pages/           # Page components
│   │   ├── lib/             # Utility functions
│   │   ├── App.tsx          # Main app component with routing
│   │   ├── main.tsx         # Entry point
│   │   └── index.css        # Global styles & theme
│   └── index.html
├── server/                  # Backend (Express + tRPC)
│   ├── routers.ts           # tRPC procedure definitions
│   ├── db.ts                # Database queries
│   └── _core/               # Core server infrastructure
├── drizzle/                 # Database schema & migrations
├── shared/                  # Shared types & constants
└── package.json
```

## Key Components

### StatusBadge
Displays job status with color coding:
- OPEN: Cyan
- ASSIGNED: Purple
- IN_PROGRESS: Yellow
- SUBMITTED: Blue
- COMPLETED: Green
- DISPUTED: Red

### JobCard
Displays job information with:
- Title and description
- Budget and deadline
- Required skills
- Status badge
- Action buttons (View Details, Apply Now)

### TxStatusModal
Shows blockchain transaction status:
- Pending: Loading spinner
- Success: Checkmark with tx hash
- Failed: Error message with retry option

### ReputationScore
Displays user reputation:
- Overall score (0-100)
- Level badge (Beginner, Intermediate, Expert, Master)
- Jobs completed
- Success rate
- Number of reviews
- Progress to next level

### NotificationBell
Notification center with:
- Unread count badge
- Notification types (success, error, info, warning)
- Timestamp formatting
- Mark as read functionality
- Clear all option

## Blockchain Integration

### Web3 Connection Flow

1. **Wallet Connection**
   - User clicks "Connect Wallet"
   - MetaMask or WalletConnect dialog appears
   - User approves connection
   - Wallet address and balance displayed

2. **SIWE Authentication** (Sign-In with Ethereum)
   - Backend generates nonce
   - User signs message with wallet
   - Backend verifies signature
   - User authenticated and logged in

3. **Smart Contract Interactions**
   - USDC Approval: User approves contract to spend USDC
   - Escrow Deposit: USDC locked in EscrowVault
   - Deliverable Commit: Hash committed on-chain
   - Dispute Vote: Arbitrator votes on dispute
   - Payment Release: Client releases payment to freelancer

### useWeb3Contract Hook

```typescript
const { txState, approveUSDC, depositToEscrow, commitDeliverableHash, voteOnDispute, releasePayment, requestRefund, resetTxState } = useWeb3Contract();

// Approve USDC
await approveUSDC(amount, spenderAddress);

// Deposit to escrow
await depositToEscrow(jobId, amount, escrowAddress);

// Commit deliverable hash
await commitDeliverableHash(jobId, deliverableHash, escrowAddress);

// Vote on dispute
await voteOnDispute(disputeId, decision, arbitratorAddress);

// Release payment
await releasePayment(jobId, escrowAddress);

// Request refund
await requestRefund(jobId, escrowAddress);
```

### useIPFSUpload Hook

```typescript
const { uploadState, uploadFile, uploadMultipleFiles, calculateFileHash, resetUploadState } = useIPFSUpload();

// Upload single file
const { cid, hash } = await uploadFile(file);

// Upload multiple files
const results = await uploadMultipleFiles([file1, file2]);

// Calculate file hash
const hash = await calculateFileHash(file);
```

## Smart Contract Addresses

Update these in `.env.local`:

```
USDC: 0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48 (Ethereum mainnet)
EscrowVault: [Deploy your contract]
Arbitrator: [Deploy your contract]
```

## IPFS Configuration

### Using Pinata

1. Create account at https://www.pinata.cloud
2. Generate API keys
3. Add to `.env.local`:
   ```
   REACT_APP_PINATA_API_KEY=your_key
   REACT_APP_PINATA_SECRET_KEY=your_secret
   ```

### Alternative Providers
- Web3.Storage: https://web3.storage
- Infura IPFS: https://infura.io
- NFT.storage: https://nft.storage

## Testing

### Unit Tests
```bash
pnpm test
```

### E2E Tests (Coming Soon)
```bash
pnpm test:e2e
```

## Deployment

### Deploy to Manus Platform

1. Create checkpoint:
   ```bash
   webdev_save_checkpoint "Production ready"
   ```

2. Click "Publish" in Management UI

3. Configure custom domain in Settings → Domains

### Deploy to External Hosting

**Vercel:**
```bash
npm i -g vercel
vercel
```

**Netlify:**
```bash
npm i -g netlify-cli
netlify deploy
```

**Railway/Render:**
- Connect GitHub repository
- Set environment variables
- Deploy

## Performance Optimization

- Lazy load pages with React.lazy()
- Code splitting with dynamic imports
- Image optimization with next/image
- CSS-in-JS optimization with Tailwind
- Database query optimization with tRPC

## Security Considerations

1. **Never expose private keys** in frontend code
2. **Use environment variables** for sensitive data
3. **Validate all user inputs** on both frontend and backend
4. **Implement rate limiting** on backend
5. **Use HTTPS** in production
6. **Implement CORS** properly
7. **Validate smart contract interactions** on backend
8. **Use secure random** for nonces/tokens

## Troubleshooting

### Wallet Connection Issues
- Check MetaMask is installed and unlocked
- Verify correct network selected
- Clear browser cache and try again

### IPFS Upload Failures
- Verify Pinata credentials
- Check file size (max 100MB)
- Ensure internet connection

### Blockchain Transaction Failures
- Check wallet has sufficient balance
- Verify contract addresses are correct
- Check gas prices and adjust if needed
- Verify network is correct

### Build Errors
```bash
# Clear cache and rebuild
rm -rf node_modules .next dist
pnpm install
pnpm build
```

## Support & Resources

- **Documentation**: [FAPEX Docs](https://docs.fapex.io)
- **Smart Contracts**: [GitHub - Blockchain](https://github.com/thanhltkk24414-lang/Blockchain)
- **Issues**: [GitHub Issues](https://github.com/thanhntm2202/fontendtest/issues)
- **Discord**: [FAPEX Community](https://discord.gg/fapex)

## License

MIT License - See LICENSE file for details

## Contributing

1. Fork the repository
2. Create feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open Pull Request

---

**Last Updated**: June 23, 2026
**Version**: 1.0.0
