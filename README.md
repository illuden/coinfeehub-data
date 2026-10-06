# CoinFeeHub open dataset

Base-tier trading fees, referral-code conditions and restricted regions for 8 crypto exchanges — each figure tied to a primary official source and a checked date.

Maintained by Alvin Lim ([coinfeehub.com/about](https://coinfeehub.com/about/)).

## What is in it

- **Exchanges (8):** Binance, Bybit, OKX, Gate, BingX, MAX, Hyperliquid, Aster.
- **Languages:** English and Simplified Chinese (`en` / `zh` text fields share one set of figures).
- **Every figure carries** the entity, product (e.g. `spot`, `futures`), unit, scope/conditions, the primary official source URL and the date that source was checked.
- **Unknown ≠ zero.** Where an official source does not state a value, the field is `null` / `unstated` — never assumed to be 0, global or permanent.
- **Snapshots:** one JSON snapshot per month in [`snapshots/`](snapshots/).

## Where to get the data

| Endpoint | What it is |
|---|---|
| `https://coinfeehub.com/data/facts.json` | **Canonical, always current.** Use this for anything live. |
| `https://coinfeehub.com/feed.xml` | Atom feed of page updates. |
| `https://coinfeehub.com/sitemap.xml` | All published pages with last-modified dates. |
| [`snapshots/YYYY-MM-DD.json`](snapshots/) | Frozen copies of `facts.json` (this repo). |

## Schema (facts.json)

Top level:

| Key | Type | Description |
|---|---|---|
| `schema_version` | integer | Currently `1`. |
| `entities` | array (8) | One object per exchange. |
| `pages` | array | Published pages: `path`, `topic`, `locale`, `title`, `description`, `prose_version`, `content_version`, `modified_at`. |

Each entity in `entities`:

| Key | Description |
|---|---|
| `id`, `name`, `kind` | Identifier (`binance`, `okx`, …), display name, `exchange`. |
| `fees[]` | Base-tier fee claims: `claim_id`, `product`, `tier`, `maker` / `taker` (`value`, `unit`, `verification`), `settlement`, `conditions_en` / `conditions_zh`, `token_discount`, `market` (regions), `status`, `starts_at` / `ends_at`, `evidence_ref`, `verified_at`. |
| `referral` | `code`, `url`, `benefits[]` — what the referral code gives, with its own conditions, regions and evidence. |
| `eligibility` | `account` and `promotion` region restrictions (`regions_status`, ISO country codes in `regions`, `note`, `evidence_ref`). |
| `legal_entity` | Contracting entity, when it can be stated. |
| `promotion_permission` | Whether the exchange permits promotion in the listed scope. |
| `onramp` | First-purchase costs (card, bank/P2P, USDT deposit, convert, withdrawal) with their own `sources`. |
| `sources[]` | Evidence records: `id`, `publisher`, `url`, `source_kind`, `verified_at`, `note`. Every `evidence_ref` above points to one of these `id`s. |
| `changes[]` | Dated change log: `changed_at`, `claim_ids`, `note`. |
| `campaigns[]` | Time-limited campaigns, if any. |
| `brand` | Display metadata for the website (colors, logo policy); not fee data. |

### Sample (OKX spot base tier)

```json
{
  "claim_id": "okx-spot-base",
  "entity_id": "okx",
  "product": "spot",
  "tier": "Base tier",
  "maker": { "value": "0.08", "unit": "percent", "verification": "verified" },
  "taker": { "value": "0.1",  "unit": "percent", "verification": "verified" },
  "conditions_en": "Regular User, global fee framework; regional schedules may differ.",
  "evidence_ref": "fees",
  "verified_at": "2026-10-06T00:00:00Z"
}
```

Values are decimal strings to avoid floating-point rounding. Resolve `evidence_ref` against the same entity's `sources[]` to get the official URL.

## How to cite

> CoinFeeHub, "Crypto exchange fees compared — <Month YYYY>", https://coinfeehub.com/compare/trading-fees/, checked <date>

See also [`CITATION.cff`](CITATION.cff).

## Update cadence

- **Daily:** an official-source collector re-fetches every source URL and flags changes for human review. An automated fetch does not renew the "checked" date; only a human re-read does.
- **Monthly:** a snapshot is added to `snapshots/` on the 1st of each month.

## Caveats

- **Base tier only.** VIP tiers, token-payment discounts, funding rates, spreads and withdrawal fees are excluded unless a field states otherwise.
- **Eligibility is decided by the exchange.** Region lists reflect the exchange's own published terms on the checked date.
- **Affiliate disclosure:** referral links and codes in this data are the operator's own referral codes. CoinFeeHub may earn a commission when they are used. Fee figures are not adjusted for this.
- Not financial advice.

## License

Data: [CC BY 4.0](LICENSE). Exchange names and trademarks belong to their respective owners.

## Contact

alvin@coinfeehub.com
