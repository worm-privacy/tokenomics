# WORM Tokenomics

⚠️ THIS IS A DRAFT DOCUMENT AND CAN CHANGE ANY TIME! ⚠️

***Reminds of Bitcoin***

- Total supply: `21,000,000 WORM`
- Half-life: `210,000 epochs` (4 years, exponential decay)
- Epoch-time: `10 mins`

- Reward after N blocks: `50 * (0.9999966993045875 ^ N)`
  - Initial reward: `50 WORM`
  - Reward after 4 years: `25 WORM`
  - Reward after 8 years: `12.5 WORM`
  - Reward after 4 * N years: `50 * (0.5^N)`

- Premine percentage: `27.865128907827067%`
- Premine: `5,851,677.070643683978082748 WORM` (Team/Foundation/Investors/Community)
- Supply after N blocks: `5,851,677.070643683978082748 + 50 * (1 - (0.9999966993045875 ^ N)) / (1 - 0.9999966993045875)`

Structure of the premine:

| Category                | % of Premine | TGE  | Cliff (Months) | Vesting (Months) |
| ----------------------- | ------------ | ---- | -------------- | ---------------- |
| LP (Liquidity Pool) (*) | 10%          | 100% | 0              | 0                |
| Private Investors       | 10%          | 0%   | 6              | 36               |
| Core Team               | 30%          | 0%   | 6              | 36               |
| Foundation Treasury     | 15%          | 5%   | 3              | 36               |
| Donors                  | 20%          | 5%   | 3              | 18               |
| Testnet Participants    | 10%          | 5%   | 3              | 12               |
| Community Members       | 5%           | 0%   | 0              | 12               |

****LP tokens are permanently locked!***

The remaining `72.13487109217293%` of the supply is mine-able through Private-Proof-of-Burn mining.

## Python simulator

```python
curr = 50 * (10**18)
num = 9999966993045875
denom = 10000000000000000
premine = 5851677070643683978082748

total = premine
while curr:
    total += curr
    curr = curr * num // denom

print(total)  # 21,000,000
```
