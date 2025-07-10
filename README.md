# ETH-KIPU-2025-TP-MOD-3

## Assigment for ETH KIPU 2025 Module 3

Simplified version of Uniswap V2 liquidity pool, limited to a single pair of ERC20 tokens

```Solidity
interface ISimpleSwap {
    function addLiquidity(
        address tokenA,
        address tokenB,
        uint256 amountADesired,
        uint256 amountBDesired,
        uint256 amountAMin,
        uint256 amountBMin,
        address to,
        uint256 deadline
    ) external returns (uint256 amountA, uint256 amountB, uint256 liquidity);

    function removeLiquidity(
        address tokenA,
        address tokenB,
        uint256 liquidity,
        uint256 amountAMin,
        uint256 amountBMin,
        address to,
        uint256 deadline
    ) external returns (uint256 amountA, uint256 amountB);

    function swapExactTokensForTokens(
        uint256 amountIn,
        uint256 amountOutMin,
        address[] calldata path,
        address to,
        uint256 deadline
    ) external ;
    // returns (uint[] memory amounts);

    function getPrice(address tokenA, address tokenB) external view returns (uint256 price);
    function getAmountOut(uint256 amountIn, uint256 reserveIn, uint256 reserveOut) external view returns (uint256);
}
```

## Users:

  There are two kinds: 

  - *Liquidity providers*: they deposit "equal value" amounts of tokens `A` and `B` into the pool (balanced according to the reserves in the pool so as to keep their product constant) and receive `LP_AB` tokens representing their share of liquidity in the pool. Likewise, when they withdraw liquidity, they burn a number of `LP_AB` tokens and receive tokens `A` and `B` in proportion to the reserves in the pool.
  
  - *Swappers*: they use the liquidity in the pool to exchange tokens; they provide, for instance, some amount of tokens `A` and receive tokens `B` according to the reserves so as to leave the pool balanced (keep the product of reserves constant). They set up a minimum acceptable of tokens `B` to receive as a rudimentary protection against slippage. 

## Functionality:

- `addLiquidity`: adds liquidity for a pool of two tokens, favouring the first desired amount (token A), calculating the balanced amount of token B to contribute to keep the usual AMM ratio `(reserve_token_A * reserve_token_B)` constant. If this amount is above the minimum desired, deposit is made, otherwise we recalculate in terms of desired amount of token B. If even then a balanced contribution is not possible, transaction reverts. If the deposit is made, a proportional amount of LP shares are minted to the address inidicated in `to`. Formulas taken from the documentation of
Uniswap V2 (<a href="https://docs.uniswap.org/contracts/v2/reference/smart-contracts/router-02"> https://docs.uniswap.org/contracts/v2/reference/smart-contracts/router-02</a>

- `removeLiquidity`: removes liquidity from a pool, burning a number of `liquidity tokens` supplied by the sender and obtaining a proportional amount of tokens `A` and `B` according to the pool reserves. They stipulate a minimum acceptable of tokens to receive as slippage protection.

- `swapExactTokensForTokens`: the user supplies a number of tokens (either `A` or`B`) and receives a proportional number of tokens according to the formula
  
```Solidity
  amountOut = (amountIn * reserveOut) / (reserveIn + amountIn)
```

  setting up a minimum accetable of tokens tokens `B` or`A` to receive as slippage protection.

- `getPrice`: yields the current price of token `A` in terms of token `B` with the formula
 
  ```Solidity
   price = (reserveB * 1e18)/ reserveA
  ```

  to account for decimals in the token (assuming the standard 18 decimals of ERC20 tokens)

- `getAmountOut`: calculates the current amount a swapper would get according to the formula quoted in the swap function.


## Dev notes:

- There are no interesting data structures involved.
- We have trimmed the output of `swapExactTokensForTokens` both to match the interface of the verifier contract
(<a href=" https://sepolia.etherscan.io/address/0x9f8f02dab384dddf1591c3366069da3fb0018220#code">  https://sepolia.etherscan.io/address/0x9f8f02dab384dddf1591c3366069da3fb0018220#code</a>) and to avoid solc problems
with "stack too deep".
- our ERC20 tokens were created with Open Zeppelin contracts wizard 
  <a href="https://wizard.openzeppelin.com/">https://wizard.openzeppelin.com/</a>

## Contract addresses (verified and published in Sepolia)
- token A: 
   <a href="https://sepolia.etherscan.io/address/0x2A4ba955C0c62AC50103A5c28C8dF4d05317a8EA">https://sepolia.etherscan.io/address/0x2A4ba955C0c62AC50103A5c28C8dF4d05317a8EA</a>

- token B: 
   <a href= "https://sepolia.etherscan.io/address/0x71822f714a0b357058360Af014Bce08EC346fBde"> https://sepolia.etherscan.io/address/0x71822f714a0b357058360Af014Bce08EC346fBde</a>
- SimpleSwap: 
   <a href="https://sepolia.etherscan.io/address/0x9f8f02dab384dddf1591c3366069da3fb0018220">https://sepolia.etherscan.io/address/0x9f8f02dab384dddf1591c3366069da3fb0018220</a>
  
