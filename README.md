# YAML trade bot

DISCLAIMER: Use at your own risk.

See `.env.example` for currently supported environment options.

## Playbooks
Bots can be configured using YAML templates known as playbooks, stored in the `~/playbook/<name>/<name>.yml` directory. Replace `<name>` with actual template name.

All `subscription` callbacks must be placed in `~/playbook/<name>/<name>.ts`

See [`~/playbook/eth-btc-mockup/eth-btc-mockup.yml`](playbook/eth-btc-mockup/eth-btc-mockup.yml) for a very simple example playbook, which would sell bearish overbought and buy bullish oversold RSI conditions of ETH/BTC, on Kraken.

### Run template
Without the YAML file extension.

```
npm run playbook <name>
```

## Items
The concept of items, refers to all core components of the bot.
Items are listed in order of dependency.

| Item | Description |
| ---- | ----------- |
| `Exchange` | Interface with external exchanges (i.e. `Kraken`, `Uniswap`, etc.) |
| `Asset` | Identifies individual assets, tokens, across the ecosystem |
| `Pair` | Two `Asset` items, tied to an `Exchange` |
| `Position` | Information such as price, associated with a `Pair` |
| `Order` | Provides actionable context on a `Pair` and `Position` |
| `Chart` | Holds dataset information for a `Pair` |
| `Analysis` | Provides contexts of configured technical analysis |
| `Scenario` | A set of conditions, for `Chart` and/or `Analysis` |
| `Strategy` | Collection of `Scenario` against a `Chart` |
| `Timeframe` | Collection of `Strategy` within time constraints |
| `Subscription` | Collection of `Timeframe`, awaiting a set of signal conditions, to do actions (callbacks) |

### Subscription actions (callbacks)
See [`~/playbook/eth-btc-mockup/eth-btc-mockup.ts`](playbook/eth-btc-mockup/eth-btc-mockup.ts) for an example set of callbacks, reference in the `eth-btc-mockup` playbook above

### Times and shorthand notation
Notations `s,m,h,d,w` (i.e. `5m` for five minutes in milliseconds) are available for better readability on `Time` (i.e. `intervalTime`) suffixed fields, otherwise values are treated as milliseconds.

```yaml
chart:
  ethBtcKraken4h:
    pair: ethBtcKraken
    pollTime: 5m          # five minutes; 300000 milliseconds
    candleTime: 4h        # four hours; 14400000 milliseconds
```

### Item referencing
All items are identified in playbooks with a `name` (names are only unique to item type), which is then used to link items together.

An example `Scenario` called `rsi14BearishOverbought`, referencing `Analysis` called `rsi14`

```yaml
analysis:
  rsi14:
    ...

scenario:
  rsi14BearishOverbought:
    analysis:
      - rsi14
    ...
```

## Environment
See `.env.example` for bot configuration options, and exchange API keys

## Testing
Mocha, Chai unit test coverage. Currently tests a known dataset for strategy scenarios, against two timeframes.
```
npm test
```

## Development
```
tsc --watch
```

## Subscribing to `Timeframe` changes
Available condition values
- `high` for the timeframe with the most amount of signals
- `low` for the timeframe with the least amount of signals
- `total` for the sum of all timeframe signals

## Caveats

### `Scenario.test()`
Currrently doesn't care how many result datasets have the same output field names. If you run conditions on field names that happen to exist in more that one of the provided datasets, it will skew your `conditionMatch` count, thus invalidating your scenario. Changing all dataset output field names, would mitigate this.