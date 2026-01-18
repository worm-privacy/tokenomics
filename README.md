# WORM Tokenomics v1.1

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

| Category                           | % of Total Supply | % of Premine | TGE               | Cliff (Months) | Vesting (Months) | Total               |
| ---------------------------------- | ----------------- | ------------ | ----------------- | -------------- | ---------------- | ------------------- |
| ⛏️ Mine-able                       | ~72.13%           | N/A          | N/A               | N/A            | N/A              | ~15,148,322.92 WORM |
| 🐫 LP / ICO                        | ~11.14%           | 40%          | 100%              | N/A            | N/A              | ~2,340,670.82 WORM  |
| 👥 Core Team                       | ~6.68%            | 24%          | 0%                | 6              | 36               | ~1,404,402.49 WORM  |
| 🗣️ Advisors                        | ~0.27%            | 1%           | 0%                | 6              | 36               | ~58,516.77 WORM     |
| 👔 Private Investors               | ~2.22%            | 8%           | 0%                | 6              | 36               | ~468,134.16 WORM    |
| 🏢 Foundation Treasury             | ~3.34%            | 12%          | 5%                | 3              | 36               | ~702,201.24 WORM    |
| 🧃 Donors (Juicebox)               | ~2.50%            | 9%           | 20%               | 3              | 18               | ~526,650.93 WORM    |
| 🧪 Testnet Participants            | ~1.53%            | 5.5%         | 50%               | 1              | 6                | ~321,842.23 WORM    |
| 🌧️ Community Activities / Airdrops | ~0.13%            | 0.5%         | 100%              | N/A            | N/A              | ~29,258.38 WORM     |


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
    Premine(premine * 30 // 100, 100, 0, 0),  # CCA
    Premine(premine * 29 // 100, 2.5, 6, 36),  # Team
    Premine(premine * 1 // 100, 2.5, 6, 36),  # Advisors
    Premine(premine * 10 // 100, 2.5, 6, 36),  # Private
    Premine(premine * 15 // 100, 5, 3, 36),  # Foundation
    Premine(premine * 9 // 100, 20, 3, 18),  # Donator
    Premine(premine * 5.5 // 100, 50, 1, 6),  # Testnet
    Premine(premine * 0.5 // 100, 100, 0, 0),  # Community
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
