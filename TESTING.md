# Validation record

Validation recorded on October 18, 2026, for the public AI Video Maker skill version **2.1.0**.

## Recorded results

| Check                                                | Result                                                  |
| ---------------------------------------------------- | ------------------------------------------------------- |
| Plugin manifest and skill frontmatter                | Passed                                                  |
| Public source-file integrity                         | All 14 files matched the verified release               |
| Runtime compatibility with the internal test harness | All six runtime scripts matched byte-for-byte           |
| JavaScript syntax                                    | All six runtime scripts passed `node --check`           |
| npm packaging dry run                                | Passed; plugin manifest and bundled skill were included |
| Internal offline regression suite                    | 34 passed, zero failed, zero skipped                    |
| Network attempts during offline regression tests     | Zero observed with an outbound network guard            |
| In-app plugin discovery and skill loading            | Passed in Cursor 3.24 using the documented local path   |
| In-app account check and live quote                  | Passed; HTTP 200, `billable: false`, no task created    |
| Paid generation                                      | Not run                                                 |
| Grok Bot catalog installation                        | Pending marketplace review                              |

The internal regression suite ran against an isolated copy of the public release. It covered mocked browser approval, expiry and denial, transient failure recovery, private credential-file handling, environment precedence, hidden terminal input, model contracts, invalid inputs, quotes, stable idempotency keys, task cancellation, terminal failures, and resumable polling timeouts. It used dummy credentials and mocked responses, with no real account connection, external API call, or video generation.

The internal regression harness is **not shipped** and cannot be rerun from this repository alone. Repository rebuild and installer suites were excluded because their supporting build and release-verification tools are absent from the public distribution.

## In-app smoke test

The plugin was copied into a new `~/.cursor/plugins/local/ai-video-maker/` directory. Cursor 3.24 discovered it under **Customize → Plugins**, displayed version **2.1.0** and the correct repository, and opened its bundled `ai-video-maker` skill. **Try in Chat** invoked the installed skill from that directory. Using an existing private connection, it ran an account check and quoted a five-second 720p MiniMax H3 Turbo video. The quote returned HTTP 200, 15 credits ($0.15 list), `billable: false`, and `taskId: null`. No video generation was authorized or submitted, and no credentials are included in this repository.

This establishes local plugin discovery, skill loading, and the account/quote path in Cursor. It does not establish paid generation or installation from the Grok Bot catalog before marketplace approval.

## Public checks reviewers can reproduce

Run these checks from this repository's root with Node.js 20 or newer. They do not load credentials, run the skill, or call the API.

Validate the manifest, skill frontmatter, and bundled source hashes:

```sh
node --input-type=module <<'JS'
import assert from "node:assert/strict";
import { readFile } from "node:fs/promises";
import { createHash } from "node:crypto";

const manifest = JSON.parse(await readFile(".cursor-plugin/plugin.json", "utf8"));
assert.equal(manifest.name, "ai-video-maker");
assert.equal(manifest.version, "2.1.0");
assert.match(manifest.name, /^[a-z0-9](?:[a-z0-9.-]*[a-z0-9])?$/);

const skill = await readFile("skills/ai-video-maker/SKILL.md", "utf8");
const frontmatter = skill.match(/^---\n([\s\S]*?)\n---\n/);
assert.ok(frontmatter);
assert.match(frontmatter[1], /^name: ai-video-maker$/m);
assert.match(frontmatter[1], /^description: \S.+$/m);

const provenance = JSON.parse(await readFile("provenance.json", "utf8"));
assert.equal(provenance.skillVersion, "2.1.0");
assert.equal(
  provenance.archiveSha256,
  "0146287b9f24dffb96b0b5721c17499b818264f06f561e88e883764c93890d8e",
);
assert.equal(provenance.files.length, 14);
for (const file of provenance.files) {
  const bytes = await readFile(file.path);
  assert.equal(createHash("sha256").update(bytes).digest("hex"), file.sha256, file.path);
}
console.log("Manifest, frontmatter, and 14 source hashes passed.");
JS
```

Check runtime syntax and inspect the proposed package contents without running lifecycle scripts or publishing:

```sh
for aivideo_script in skills/ai-video-maker/scripts/*.mjs; do
  node --check "$aivideo_script" || exit 1
done
npm pack --dry-run --ignore-scripts
```

These commands reproduce packaging checks only. They do not reproduce the internal 34-test regression suite. The source archive and its published checksum are linked in `provenance.json`; the [official Cursor local testing guide](https://cursor.com/docs/plugins#test-plugins-locally) describes the separate in-app check. To repeat the live quote check, use your own private connection and run the skill's `doctor` and `quote` actions; do not authorize generation merely to test installation.
