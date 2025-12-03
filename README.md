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

| Category                | % of Premine | % of Total Supply | TGE               | Cliff (Months) | Vesting (Months) | Total               |
| ----------------------- | ------------ | ----------------- | ----------------- | -------------- | ---------------- | ------------------- |
| Mine-able               | N/A          | ~72.13%           | N/A               | N/A            | N/A              | ~15,148,322.92 WORM |
| Uniswap CCA             | 30%          | ~2.78%            | 100%              | N/A            | N/A              | ~1,755,503.12 WORM  |
| Core Team               | 30%          | ~8.35%            | 0%                | 6              | 36               | ~1,755,503.12 WORM  |
| Private Investors       | 10%          | ~2.78%            | 0%                | 6              | 36               | ~585,167.70 WORM    |
| Foundation Treasury     | 15%          | ~4.17%            | 5%                | 3              | 36               | ~877,751.56 WORM    |
| Donors                  | 9.5%         | ~3.48%            | 20%               | 3              | 18               | ~555,909.32 WORM    |
| Testnet Participants    | 5%           | ~2.78%            | 50%               | 1              | 6                | ~292,583.85 WORM    |
| Community Activities    | 0.5%         | ~0.69%            | 100%              | N/A            | N/A              | ~29,258.38 WORM     |

The remaining `72.13487109217293%` of the supply is mine-able through Private-Proof-of-Burn mining.

## Python simulator

```python
curr = 50 * (10**18)
num = 9999966993045875
denom = 10000000000000000
premine = 5851677070643683978082748
total_supply = 21000000000000000000000000


class Premine:
    def __init__(self, amount, tge, cliff, vesting):
        self.amount = amount
        self.tge = tge
        self.cliff = cliff
        self.vesting = vesting

    def released(self, month):
        tge = int(self.amount * self.tge / 100.0)
        rest = self.amount - tge
        total = tge
        if month >= self.cliff:
            if self.vesting > 0 and month < self.vesting:
                total += min(rest * month / self.vesting, rest)
            else:
                total += rest
        return int(total)


premines = [
    Premine(premine * 10 // 100, 100, 0, 0),  # LP
    Premine(premine * 10 // 100, 0, 6, 36),  # Private
    Premine(premine * 30 // 100, 0, 6, 36),  # Team
    Premine(premine * 15 // 100, 5, 3, 36),  # Foundation
    Premine(premine * 15 // 100, 5, 4, 18),  # Donator
    Premine(premine * 7.5 // 100, 5, 2, 12),  # Testnet
    Premine(premine * 2.5 // 100, 0, 0, 12),  # Community
]


def circulating_premine(month):
    return sum([p.released(month) for p in premines])


month = 0.0
previous_month = 0
total_mined = 0
while curr:
    total_mined += curr
    curr = curr * num // denom
    month += 1 / (6 * 24 * 30)

    if int(month) != previous_month:
        if month < 120:
            total_circulating = total_mined + circulating_premine(month)
            print(
                int(month),
                ",",
                total_mined / total_circulating,
                ",",
                total_circulating / total_supply,
            )
            previous_month = int(month)


print(total_mined + circulating_premine(36))  # 21,000,000
```
