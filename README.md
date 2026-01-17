# WORM Tokenomics v0.1.0

⚠️ THIS IS A DRAFT DOCUMENT AND CAN CHANGE ANY TIME! ⚠️

***Reminds of Bitcoin***

- Total supply: `21,000,000 WORM`
- Half-life: `210,000 epochs` (4 years, exponential decay)
- Epoch-time: `10 mins`

- Reward after N blocks: `25 * (0.9999966993045875 ^ N)`
  - Initial reward: `25 WORM`
  - Reward after 4 years: `12.5 WORM`
  - Reward after 8 years: `6.25 WORM`
  - Reward after 4 * N years: `25 * (0.5^N)`

- Premine percentage: `63.93256445391702%`
- Premine: `13,425,838.535322574637506235`
- Supply after N blocks: `13,425,838.535322574637506235 + 25 * (1 - (0.9999966993045875 ^ N)) / (1 - 0.9999966993045875)`

Structure of the premine:

| Category                           | % of Total Supply | % of Premine | TGE               | Cliff (Months) | Vesting (Months) | Total               |
| ---------------------------------- | ----------------- | ------------ | ----------------- | -------------- | ---------------- | ------------------- |
| ⛏️ Mine-able                       | ~36.06%           | N/A          | N/A               | N/A            | N/A              | ~15,148,322.92 WORM |
| 🦄 ICO / LP                        | ~44.46%           | 69.54%          | 100%              | N/A            | N/A              | ~9,336,328.11 WORM  |
| 👥 Core Team                       | ~8.07%            | 12.63%          | 2.5%              | 6              | 36               | ~1,755,503.12 WORM  |
| 🗣️ Advisors                        | ~0.27%            | 0.43%           | 2.5%              | 6              | 36               | ~1,755,503.12 WORM  |
| 👔 Private Investors               | ~2.78%            | 4.35%          | 2.5%              | 6              | 36               | ~585,167.70 WORM    |
| 🏢 Foundation Treasury             | ~4.17%            | 6.53%          | 5%                | 3              | 36               | ~877,751.56 WORM    |
| 🧃 Donors (Juicebox)               | ~2.50%            | 3.92%           | 20%               | 3              | 18               | ~555,909.32 WORM    |
| 🧪 Testnet Participants            | ~1.52%            | 2.39%         | 50%               | 1              | 6                | ~292,583.85 WORM    |
| 🌧️ Community Activities / Airdrops | ~0.13%           | 0.21%         | 100%              | N/A            | N/A              | ~29,258.38 WORM     |

The remaining `72.13487109217293%` of the supply is mine-able through Private-Proof-of-Burn mining.

## Python simulator

```python
curr = 50 * (10**18)
num = 9999966993045875
denom = 10000000000000000
premine = 13425838535322574637506235
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
    Premine(premine * 69.54  // 100, 100, 0, 0),  # CCA
    Premine(premine * 12.63 // 100, 0, 6, 36),  # Team
    Premine(premine * 0.43 // 100, 0, 6, 36),  # Advisors
    Premine(premine * 4.35 // 100, 0, 6, 36),  # Private
    Premine(premine * 6.53 // 100, 5, 3, 36),  # Foundation
    Premine(premine * 3.92 // 100, 20, 3, 18),  # Donator
    Premine(premine * 2.39 // 100, 50, 1, 6),  # Testnet
    Premine(premine * 0.21 // 100, 100, 0, 0),  # Community
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
