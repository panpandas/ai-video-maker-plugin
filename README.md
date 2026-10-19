# AI Video Maker

A skill-only Cursor/Grok Bot plugin wrapping the public AI Video Maker **2.1.0** release. Create videos from text, images, frames, or references with cost quotes, approved spending limits, and resumable task handling.

## Requirements and pricing

- Node.js 20 or newer and network access to AI Video Maker.
- An AI Video Maker account and each user's own private connection.
- Installation is free. Video generation consumes API credits; the live server quote establishes the price. Obtain spending approval before generation. An API subscription is not required.

## Install and connect

After marketplace approval, install AI Video Maker through the host's marketplace. In Cursor, open **Customize**, select the plugin, then **Install**. In Grok Bot, use **Connect apps** and review **Your plugins**. This repository does not establish marketplace availability or approval.

For Cursor local development, copy the plugin into a new `~/.cursor/plugins/local/ai-video-maker/` directory, preserving `.cursor-plugin/plugin.json` and `skills/`. Restart Cursor or run **Developer: Reload Window**, then confirm the skill appears in **Customize**. Review existing installations before replacing them. Local plugin imports must be allowed by the host or administrator.

Read `skills/ai-video-maker/SKILL.md`. Resolve its installed directory to an absolute `SKILL_ROOT`, then run:

```sh
node "$SKILL_ROOT/scripts/connect.mjs"
```

The helper shows a browser approval link and comparison code. Each user signs in and approves their own code. The helper saves the connection privately and verifies the account and a sample quote without spending credits. Never paste API keys into chat, source, URLs, logs, or shared configuration. Never include a saved connection in a template.

## Use

Ask the agent to prepare a prompt and quote the exact cost. Generate only after approval of that spend or a previously authorized sufficient budget. Enforce `--maxCredits`, preserve the task ID and idempotency key, and resume the original task after a timeout. Retrieve the finished video and save it within 24 hours. Cancellation is available only while the API permits it.

## Provenance and testing

Bundled skill files are copied byte-for-byte from the [public 2.1.0 archive](https://aivideomaker.ai/skills/ai-video-maker-2.1.0.tgz), verified against its [published SHA-256 checksum](https://aivideomaker.ai/skills/ai-video-maker-2.1.0.tgz.sha256). Source and file hashes are recorded in `provenance.json`.

Packaging checks passed for the manifest, skill frontmatter, all 14 public source-file hashes, JavaScript syntax, and an npm packaging dry run. A separate internal regression harness passed **34/34 offline tests**, with zero observed network attempts. All six runtime scripts matched the version covered by that harness. Tests used mocks and dummy credentials; no real account connection, API call, or video generation was performed.

The internal regression harness is not shipped. Repository rebuild and installer suites were excluded because the public distribution does not include their supporting build and verification tools. In Cursor 3.24, the local plugin was discovered as version 2.1.0, its bundled skill opened successfully, and **Try in Chat** ran a successful account check and a live, non-billable MiniMax quote using an existing private connection. The quote returned HTTP 200, `billable: false`, and no task ID. No generation was submitted. Grok Bot catalog installation awaits marketplace review. See [TESTING.md](TESTING.md) for scope, limitations, and public packaging checks reviewers can reproduce.

## Resources

- [Product and setup](https://aivideomaker.ai/skill)
- [Live setup overview](https://aivideomaker.ai/skill.md)
- [API quickstart](https://aivideomaker.ai/docs/api/quickstart)
- [Cursor plugin format](https://cursor.com/docs/reference/plugins)
- [Cursor local testing](https://cursor.com/docs/plugins#test-plugins-locally)

Support: support@aivideomaker.ai. License: MIT-0, as specified in the public skill card.
