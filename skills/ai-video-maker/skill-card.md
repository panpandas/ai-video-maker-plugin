## Description

Give Codex, Claude Code, or OpenClaw a guarded AI video workflow. The skill validates setup, quotes the exact API charge, obtains spending approval, creates a task with idempotency protection, polls safely, and returns the completed video URL.

MiniMax H3 supports text-to-video, image-to-video, start/end-frame guidance, and reference-guided generation with native audio controls. The skill also supports Seedance 2.0, Wan 3.0, and HappyHorse 1.1; see the included API reference.

## Publisher

[husu](https://clawhub.ai/user/husu)

## License/Terms of Use

MIT-0

## Requirements

- Node.js 20 or newer
- A browser-approved connection started by `node "$SKILL_ROOT/scripts/connect.mjs"`, or `AIVIDEO_API_KEY` in the agent’s secret environment. The helper saves the key privately outside your project and verifies setup without spending credits. Hidden terminal key entry remains available with `--manual`.
- An AI Video Maker account; the first API key includes 50 introductory API credits

## Pricing

The skill is free to install. Generated videos use API credits at a list price of USD $0.01 per credit.

MiniMax H3 pricing:

| Mode        | Credits/second | USD/second |
| ----------- | -------------: | ---------: |
| 720p Turbo  |              3 |      $0.03 |
| 720p Base   |              4 |      $0.04 |
| 1080p Turbo |              4 |      $0.04 |
| 1080p Base  |              5 |      $0.05 |

A five-second 720p Turbo video costs 15 credits ($0.15). The server quote is authoritative for every request. One-time credit packs are available without an API subscription.

## Use Cases

- Generate short videos from a prompt inside an agent workflow.
- Animate a product, character, or reference image.
- Build content automations without exposing the API key in source code.
- Estimate generation cost before authorizing a billable action.
- Resume a timed-out task without creating a duplicate charge.

## Cost and Reliability Controls

- Non-billable `doctor` and `quote` commands
- Caller-approved `maxCredits` enforced locally and by the API
- Stable idempotency keys for safe retries
- Per-key daily credit, request-rate, and low-balance controls
- Refunded credits for terminal provider failures
- Resumable polling timeout with the original task ID

## Known Risks and Mitigations

Risk: A generation request consumes API credits.

Mitigation: Quote before submission, obtain approval, and set `maxCredits` to the approved limit.

Risk: Retrying an ambiguous request could create a duplicate task.

Mitigation: Reuse the same idempotency key for the identical request and resume a known task by task ID.

Risk: Prompts, media inputs, and task metadata are sent to AI Video Maker.

Mitigation: Avoid sensitive media, use a dedicated API key, configure key-level limits, and rotate the key when needed.

Risk: Completed output files are retained for 24 hours.

Mitigation: Copy completed videos to durable storage promptly.

## References

- [Create an API key](https://aivideomaker.ai/app/settings/account/api-keys?utm_source=agent_skill&utm_medium=skill&utm_campaign=ai_video_maker)
- [Skill landing page and pricing](https://aivideomaker.ai/skill?utm_source=clawhub&utm_medium=directory&utm_campaign=ai_video_maker)
- [API quickstart](https://aivideomaker.ai/docs/api/quickstart)
- [Included API reference](references/api-reference.md)
- [Included examples](references/examples.md)

## Skill Output

**Output types:** JSON, shell commands, and workflow guidance.

Normalized results include `ok`, `status`, `taskId`, `credits`, `data`, `errorCode`, `errorMessage`, `retryAfter`, and the idempotency key when applicable.

## Skill Version

2.1.0
