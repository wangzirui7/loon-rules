# Loon routing rules

This repository contains **rules only** for the following routing design:

| Traffic | Loon policy group | Nodes allowed |
| --- | --- | --- |
| ChatGPT / OpenAI | `AI-好` | Good subscription only |
| PressReader | `PressReader-日本-差` | Japan nodes from the lower-priority subscription only |
| Other domestic traffic | `DIRECT` | Direct |
| Other unmatched international traffic | `普通代理-差` | Lower-priority subscription only |

No subscription URL, node address, password, UUID, or other credential belongs in this repository.

## Rule-set URLs

- OpenAI core: `https://raw.githubusercontent.com/wangzirui7/loon-rules/main/Rules/OpenAI-Core.list`
- PressReader: `https://raw.githubusercontent.com/wangzirui7/loon-rules/main/Rules/PressReader.list`
- Minimal China direct rule: `https://raw.githubusercontent.com/wangzirui7/loon-rules/main/Rules/China-Direct.list`

In Loon, open **Configuration → Subscription Rules → +**, paste one URL, choose the corresponding policy group, and save.

Order the rule sets like this:

1. OpenAI core → `AI-好`
2. PressReader → `PressReader-日本-差`
3. China direct → `DIRECT`
4. Your general international rule / final rule → `普通代理-差`

## Policy groups

Create these three groups before adding the rules:

- `AI-好`: `select`; contains **only nodes from the good subscription**.
- `PressReader-日本-差`: `select`; contains **only Japanese nodes from the lower-priority subscription**. Do not add `DIRECT`.
- `普通代理-差`: `select` or `url-test`; contains **only nodes from the lower-priority subscription**.

The good subscription must not appear in `PressReader-日本-差`, `普通代理-差`, or any global/final group.

## Text-profile fallback

If you manage a text Loon profile, append the contents of [Loon/routing-rules.conf.example](Loon/routing-rules.conf.example) to its `[Rule]` section. Place the service-specific rules above any broad global/Google/China rule.

## Why the lists are intentionally narrow

The OpenAI list excludes shared domains such as `stripe.com`, `intercom.io`, `sentry.io`, `cloudflare.com`, and `amazonaws.com`; sending those to `AI-好` would allow unrelated apps to consume the good subscription.

The PressReader list includes PressReader-owned domains only. Its official network documentation also names broad infrastructure domains, but those are deliberately excluded for the same reason.

## Verification

Use Loon in **Rule** mode, then check its log:

- ChatGPT should show policy `AI-好`.
- PressReader should show policy `PressReader-日本-差`.
- A Chinese app/site should show `DIRECT`.
- An unmatched foreign site should show `普通代理-差`.

If a necessary request misses a service policy, add the **exact hostname** from the Loon log; do not add a shared parent CDN domain.
