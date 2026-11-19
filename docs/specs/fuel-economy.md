# Spec: Fuel Economy & Cost Calculations

| | |
|---|---|
| **Status** | Draft v0.1 |
| **Implements** | FR-020, FR-024, FR-026, FR-050, FR-051, FR-053 |
| **Code location** | `packages/domain/src/economy/` (pure functions, no I/O) |
| **Last updated** | 2026-11-19 |

Accurate numbers are the core promise of Driveline. This spec defines every calculation precisely so that the implementation, the tests and the help text all agree. **The golden test cases in §7 are normative**: the implementation must reproduce them exactly.

## 1. Principles

1. **Full-tank method.** Economy is only measured between two full-tank fill-ups. This is the only method that is accurate without knowing the exact fuel level.
2. **Never guess.** If data needed for an interval is missing (a missed fill-up), that interval produces no economy figure instead of a wrong one.
3. **Weighted averages.** Averages are total distance ÷ total fuel, never the average of per-interval figures.
4. **Store in base units, display in user units.** Litres, kWh, distance in the vehicle's odometer unit, money in integer minor units. Conversion happens only at the presentation layer.
5. **Order by odometer.** Entries are ordered by odometer, then by timestamp as a tie-breaker. Users may log out of order.

## 2. Inputs

Each fill-up `f` for a given vehicle and **fuel tank** has:

| Field | Type | Notes |
|---|---|---|
| `odometer` | decimal ≥ 0 | Vehicle's odometer unit |
| `volume` | decimal > 0 | Litres |
| `isFull` | boolean | Tank filled to the top |
| `missedPrevious` | boolean | One or more fill-ups before this one were not logged |
| `totalCost` | integer | Minor units |

## 3. Fuel economy algorithm (single tank)

```
sort fill-ups by (odometer, timestamp)
baseline = null
pendingFuel = 0
broken = false

for each fill-up f:
    if baseline is null:
        if f.isFull: baseline = f
        continue                       # first full tank only sets the baseline

    pendingFuel += f.volume
    if f.missedPrevious: broken = true

    if f.isFull:
        if not broken:
            distance = f.odometer - baseline.odometer
            emit Interval(start=baseline, end=f, distance, fuel=pendingFuel)
        else:
            emit BrokenInterval(start=baseline, end=f)   # shown, but no economy
        baseline = f
        pendingFuel = 0
        broken = false
```

Partial fill-ups before the first full fill-up are ignored for economy (they still count toward cost).

### Derived figures for an interval

| Figure | Formula |
|---|---|
| Distance per volume (mpg, km/L) | `distance ÷ fuel` (after unit conversion) |
| Volume per 100 distance (L/100 km) | `100 × fuel ÷ distance` |
| Fuel cost per distance | `Σ cost of fill-ups in interval ÷ distance` |

### Invalid intervals

An interval with `distance ≤ 0` is reported as a **data error** (likely a mistyped odometer) and excluded from averages. FR-024 warnings should normally prevent this.

## 4. Averages and reports

- **Average economy** over a period = `Σ distance ÷ Σ fuel` across valid intervals whose **end** fill-up falls in the period.
- **Last economy** = the most recent valid interval.
- **Best / worst** = max / min over valid intervals in the period.

## 5. Multi-energy vehicles

| Configuration | Rule |
|---|---|
| **Single fuel** | One tank chain as in §3. |
| **Bi-fuel** (e.g. gasoline + LPG) | One independent chain per tank. Because the split of distance between fuels is unknown, per-fuel economy is labelled "per distance driven while both fuels were in use". The headline figure is **combined energy cost per distance**. |
| **Plug-in hybrid** | Liquid fuel uses §3. Electric consumption = `Σ kWh ÷ Σ distance` over the period (not per charge). Headline figures: fuel per 100 distance, kWh per 100 distance, combined cost per distance (FR-053). |
| **Battery electric** | Consumption over a period = `Σ kWh ÷ distance driven in the period`. If start/end battery % is supplied on consecutive charges, per-interval consumption may be shown as an estimate (marked "estimated"). Charging losses are included because kWh is measured at the charger. |

## 6. Cost calculations

| Figure | Definition |
|---|---|
| **Distance driven in a period** | `max odometer in period − last odometer before the period`. If no earlier entry exists, use the min odometer in the period. |
| **Running cost per distance** | `Σ all entry costs in period (fuel, charge, expense, service) ÷ distance driven`. Income is excluded. |
| **Net cost** (gig drivers) | `Σ costs − Σ income` |
| **Monthly cost** | Sum of costs whose timestamp falls within the calendar month in the user's time zone. |

## 7. Golden test cases (normative)

Units: miles and US gallons. Expected values are rounded to 2 decimals for display only; tests compare with a tolerance of 1e-9 on raw values.

### Case A — Full, partial, missed

| # | Odometer | Gallons | Full | Missed | Expected result |
|---|---|---|---|---|---|
| 1 | 10,000 | 10.0 | ✔ | | Baseline, no economy |
| 2 | 10,300 | 10.0 | ✔ | | 300 ÷ 10 = **30.00 mpg** |
| 3 | 10,450 | 5.0 | | | Partial: accumulates |
| 4 | 10,600 | 6.0 | ✔ | | 300 ÷ 11 = **27.27 mpg** |
| 5 | 10,900 | 10.0 | ✔ | ✔ | **Broken** interval, no economy; becomes new baseline |
| 6 | 11,200 | 10.0 | ✔ | | 300 ÷ 10 = **30.00 mpg** |

Average economy = (300 + 300 + 300) ÷ (10 + 11 + 10) = 900 ÷ 31 = **29.03 mpg** (not the mean of 30, 27.27, 30 = 29.09).

### Case B — Out-of-order entry

Same as Case A but entry 3 is logged last (timestamp after entry 6). Results must be identical to Case A.

### Case C — Partials before first full

| # | Odometer | Gallons | Full | Expected |
|---|---|---|---|---|
| 1 | 5,000 | 4.0 | | Ignored for economy |
| 2 | 5,120 | 8.0 | ✔ | Baseline |
| 3 | 5,420 | 10.0 | ✔ | **30.00 mpg** |

### Case D — Metric conversion

Same data as Case A, entry 2 (300 mi, 10 US gal) displayed in metric:
- 300 mi = 482.8032 km; 10 US gal = 37.85411784 L
- **7.84 L/100 km**, **12.75 km/L**

### Case E — Data error

Full fill-ups at odometer 8,000 then 7,900 (user typo). Interval distance −100 → flagged as data error, excluded from averages, and FR-024 warning was shown at entry time.

## 8. Unit conversion constants

| Conversion | Exact value |
|---|---|
| 1 mile | 1.609344 km |
| 1 US gallon | 3.785411784 L |
| 1 imperial gallon | 4.54609 L |

## 9. Change log

| Version | Date | Change |
|---|---|---|
| 0.1 | 2026-11-19 | Initial draft |
