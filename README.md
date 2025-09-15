# V4 Swap and Bridge Optimism Router

A Uniswap V4 router that enables atomic swaps and bridging to Optimism L2 in a single transaction. This contract allows users to swap tokens on Ethereum mainnet/testnet and automatically bridge the output tokens to Optimism.

## Features

- **Atomic Swap + Bridge**: Swap tokens on Uniswap V4 and bridge the output to Optimism in one transaction
- **ETH and ERC20 Support**: Handles both native ETH and ERC20 token bridging
- **Flexible Recipients**: Bridge tokens to the same address or a different recipient on L2
- **Owner-managed Token Mapping**: Configurable L1↔L2 token address mappings

## Architecture

The router integrates with:
- **Uniswap V4 Pool Manager**: For executing swaps
- **Optimism L1 Standard Bridge**: For bridging assets to L2
- **OpenZeppelin Ownable**: For access control

## Prerequisites

- [Foundry](https://book.getfoundry.sh/getting-started/installation)
- An RPC endpoint for Ethereum Sepolia (e.g., Alchemy, Infura)

## Installation

```bash
# Clone the repository
git clone <repository-url>
cd v4-swap-bridge-op-router

# Install dependencies
forge install

# Build the project
forge build
```

## Configuration

### RPC Setup
Update the test file with your RPC endpoint:

```solidity
// In test/TestSwapAndBridgeOptimismRouter.t.sol
uint256 sepoliaForkId = vm.createFork("https://eth-sepolia.g.alchemy.com/v2/YOUR_API_KEY");
```

Or configure in `foundry.toml`:
```toml
[rpc_endpoints]
sepolia = "https://eth-sepolia.g.alchemy.com/v2/YOUR_API_KEY"
```

## Usage

### Build

```shell
forge build
```

### Test

```shell
# Run all tests
forge test

# Run with verbosity
forge test -vvv

# Run specific test
forge test --match-test test_swapETHForOUTb_bridgeTokensToOptimism
```

### Format

```shell
forge fmt
```

### Deploy

```shell
forge script script/SwapAndBridgeOptimismRouterScript.s.sol:SwapAndBridgeOptimismRouterScript --rpc-url sepolia --private-key <your_private_key> --broadcast
```

## Contract Interface

### Core Functions

#### `swap()`
```solidity
function swap(
    PoolKey memory key,
    SwapParams memory params,
    SwapSettings memory settings,
    bytes memory hookData
) external payable returns (BalanceDelta delta)
```

Executes a swap and optionally bridges the output tokens to Optimism.

**Parameters:**
- `key`: Uniswap V4 pool key
- `params`: Swap parameters (direction, amount, price limit)
- `settings`: Bridge settings (enable bridging, recipient address)
- `hookData`: Additional data for hooks

#### `addL1ToL2TokenAddress()`
```solidity
function addL1ToL2TokenAddress(address l1Token, address l2Token) external onlyOwner
```

Maps L1 token addresses to their L2 counterparts for bridging.

### Structs

#### `SwapSettings`
```solidity
struct SwapSettings {
    bool bridgeTokens;      // Whether to bridge output tokens
    address recipientAddress; // L2 recipient address
}
```

## Testing

The test suite covers:
- ✅ ETH → ERC20 swaps with bridging
- ✅ ERC20 → ETH swaps with bridging
- ✅ Swaps without bridging
- ✅ Different recipient scenarios
- ✅ Event emission verification

### Test Results
```
Ran 6 tests for test/TestSwapAndBridgeOptimismRouter.t.sol:TestSwapAndBridgeOptimismRouter
[PASS] test_swapETHForOUTb_bridgeTokensToOptimism_receipientNotSameAsSender()
[PASS] test_swapETHForOUTb_bridgeTokensToOptimism_recipientSameAsSender()
[PASS] test_swapETHForOUTb_dontBridgeTokens()
[PASS] test_swapOUTbForETH_bridgeTokensToOptimism_recipientNotSameAsSender()
[PASS] test_swapOUTbForETH_bridgeTokensToOptimism_recipientSameAsSender()
[PASS] test_swapOUTbForETH_dontBridgeTokens()
```

## Dependencies

- **Uniswap V4 Core**: Pool management and swapping
- **Uniswap V4 Periphery**: Router utilities
- **OpenZeppelin Contracts**: Access control (Ownable)
- **Optimism Contracts**: L1 Standard Bridge interface
- **Foundry**: Development framework

## Network Addresses

### Ethereum Sepolia
- **L1 Standard Bridge**: `0xFBb0621E0B23b5478B630BD55a5f21f67730B0F1`
- **Test Token (OUTb)**: `0x12608ff9dac79d8443F17A4d39D93317BAD026Aa`

### OP Sepolia
- **Test Token (OUTb)**: `0x7c6b91D9Be155A6Db01f749217d76fF02A7227F2`

## Gas Usage

Approximate gas costs:
- Swap without bridging: ~118,000 gas
- Swap with ETH bridging: ~547,000 gas
- Swap with ERC20 bridging: ~527,000 gas

## Security Considerations

- The contract uses OpenZeppelin's `Ownable` for access control
- Token mappings must be set by the owner before bridging
- The contract validates that tokens can be bridged before executing swaps
- Excess ETH is returned to the sender

## License

MIT

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests
5. Run `forge test` to ensure all tests pass
6. Submit a pull request

## Troubleshooting

### Common Issues

**RPC Connection Errors**: Ensure your RPC endpoint is working and has sufficient rate limits.

**Test Failures**: Make sure you're using a valid Sepolia RPC endpoint in the test file.

**Compilation Errors**: Run `forge clean && forge build` to clear cache and rebuild.

## Resources

- [Uniswap V4 Documentation](https://docs.uniswap.org/contracts/v4/overview)
- [Optimism Bridge Documentation](https://docs.optimism.io/builders/app-developers/bridging/standard-bridge)
- [Foundry Book](https://book.getfoundry.sh/)