# Will the Customer Accept the Coupon?

An exploratory analysis of which drivers accept location-based coupons delivered to
their phone, and which ignore them.

## The question

Imagine driving through town when a coupon for a nearby restaurant arrives on your
phone. Do you take the detour? Does the answer change if it's a bar instead of a
coffee shop, if your kids are in the back seat, or if it's pouring rain?

This project looks at survey responses from 12,684 driving scenarios to find what
actually separates the people who say yes from the people who say no.

## The data

Collected via Amazon Mechanical Turk and published by the UCI Machine Learning
Repository. Each row describes a driving scenario — destination, time of day,
weather, who's in the car — plus the driver's demographics and how often they
already visit bars, coffee houses, and restaurants. Drivers who said they'd take
the coupon either right away or before it expired are counted as accepting.

Five coupon types appear: coffee houses, cheap restaurants (under $20), carry-out,
bars, and pricier restaurants ($20–50).

## Headline finding

**What people already do predicts what they'll accept. Who they are mostly doesn't.**

The strongest signal in the entire dataset is whether someone already visits that
kind of venue:

| Group | Accepts |
|---|---|
| Visits bars more than 3x/month | ~76% |
| Visits bars 3x/month or fewer | ~37% |
| Visits coffee houses 4–8x/month | ~68% |
| **Never** visits coffee houses | **~18%** |

That last row is the most useful number here. People who said they never buy
coffee out turned down nearly five out of every six coffee coupons — the worst
converting group found anywhere in the analysis.

Age, income, marital status, and education all produced much smaller gaps. Where
they appeared to matter, it was largely because they stood in for the underlying
habit: a 45-year-old who never goes to bars is a poor target, while a 27-year-old
who goes weekly is a good one.

## What else moved the needle

Unlike demographics, these are things a delivery system can actually control:

- **Time of day** — coffee coupons sent at 10AM were accepted ~63% of the time
  versus ~41% at 6PM. Same coupon, same people, twenty-two points of difference.
- **Where they're headed** — drivers with no urgent destination accepted ~58% of
  coffee coupons; drivers headed home accepted ~36%.
- **How long the coupon lasts** — a one-day window beat a two-hour window by
  roughly 15 points. Urgency seems to push people away rather than hurry them along.
- **Who's in the car** — riding with friends beat driving alone by ~16 points.

Coupon type also sets a ceiling. Overall acceptance ran about 57%, but bar coupons
managed only ~41% and coffee ~50%. Bar coupons are simply the hardest sell.

## Recommendations

1. **Target on behavior, not demographics.** Prior venue visits are the single most
   valuable targeting signal available.
2. **Stop sending to the "never" group.** They convert below 20%. Withholding those
   sends costs almost nothing and reduces notification fatigue.
3. **Send coffee coupons in the morning.** Same inventory, ~22 points better.
4. **Default to the longer expiration window.** Short windows suppressed acceptance.
5. **Favor non-urgent trips.** Drivers headed home are the worst audience.

## Honest limitations

These are **survey answers about hypothetical scenarios, not real redemptions.**
People routinely overstate what they'd do, so treat these rates as relative
comparisons rather than forecasts of actual response.

The analysis is also **descriptive and looks at one variable at a time.** Because
the variables are tangled together — younger drivers go to bars more often — these
comparisons can't cleanly separate one effect from another. No significance testing
was run, and a few segments are small enough to be unstable: the highest-frequency
bar group has fewer than 50 observations, which is why its slight dip below the
next group down shouldn't be read as meaningful.

**Next steps** would be significance tests on the main comparisons, a logistic
regression to isolate independent effects, and validation against real redemption
data before any of this drives live targeting.

## Repository contents

```
├── prompt.ipynb              # original starter notebook (unmodified)
├── prompt_completed.ipynb    # completed analysis
├── data/
│   └── coupons.csv           # UCI coupon dataset
└── README.md
```

## Running it

```bash
pip install pandas numpy matplotlib seaborn jupyter
jupyter notebook prompt_completed.ipynb
```

Run top to bottom from a fresh kernel (Kernel → Restart & Run All).

## Notes on method

`car` was dropped — 99% of its values were missing. Five venue-frequency columns
had 0.8–1.7% missing values; since these are ordinal categories (`never`, `less1`,
`1~3`, `4~8`, `gt8`) rather than numbers, averaging them would be meaningless, so
those rows were dropped instead. Along with 74 duplicate rows, this left 12,007 of
the original 12,684 observations.

One judgment call worth flagging: the `age` column is bucketed, and the `21` bucket
spans ages 21–25. The question asking about drivers "over the age of 25" was
therefore read as excluding that bucket.

## Source

Dua, D. and Graff, C. (2019). *UCI Machine Learning Repository.* Irvine, CA:
University of California, School of Information and Computer Science.
