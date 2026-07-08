---
description: "Manually test the smollm2 inference snap from a chosen Snap Store channel and file an issue if something is broken."

on:
  workflow_dispatch:
    inputs:
      snap-channel:
        description: "Snap Store channel to install smollm2 from (e.g. latest/stable, latest/edge, latest/edge/pr-123)"
        required: true
        type: string
        default: latest/edge

engine: copilot

strict: false

runs-on: ubuntu-latest

permissions:
  contents: read

# The agent must install and run a strictly-confined snap, which requires the host's
# snapd daemon and passwordless sudo. Neither is available inside the AWF sandbox
# container (it has no snapd socket and sets NoNewPrivs=1, which blocks sudo), so the
# agent is run directly on the trusted, manually-triggered GitHub-hosted runner instead.
sandbox:
  agent: false

features:
  dangerously-disable-sandbox-agent: "Snap install and running strictly-confined snap commands require host snapd and passwordless sudo, which are unavailable inside the AWF sandbox container. Triggered manually by maintainers on trusted input."

tools:
  bash: [":*"]
  web-fetch:

timeout-minutes: 30

safe-outputs:
  threat-detection: false
  create-issue:
    title-prefix: "[ai-testing] "
    labels: [ai-testing, bug]
    max: 1
    close-older-issues: true
---

# Automated testing of the smollm2 inference snap

You are a user of our snap, testing the `smollm2` inference snap installed from the
Snap Store channel **`${{ github.event.inputs.snap-channel }}`**.

This is an inference snap. These snaps are documented at
https://documentation.ubuntu.com/inference-snaps. Read the documentation to learn how
to install and use them before you start.

## Setup

You are running directly on a GitHub-hosted Ubuntu runner with a working `snapd` and
passwordless `sudo`, so install and use the snap exactly as a real user would.

1. Install the snap from the requested channel:

   ```
   sudo snap install smollm2 --channel="${{ github.event.inputs.snap-channel }}"
   ```

   Pass the channel string exactly as given (it may include a track/risk/branch).

2. Confirm it installed and inspect its interface connections:

   ```
   snap list smollm2
   snap connections smollm2
   ```

   This snap is strictly confined. Some plugs (e.g. `hardware-observe`) may not
   auto-connect. If a check below fails because of a missing connection, connect it
   with `sudo snap connect smollm2:<plug>` and note that this was required.

3. Discover the available commands before using them:

   ```
   smollm2 --help
   ```

## Things to check

Test the snap the way a user would, using its own commands:

- The snap installs correctly and appears in `snap list`.
- An engine is automatically selected — inspect with `smollm2 list-engines` (and any
  related status/info command you discover from `smollm2 --help`).
- The server daemon is running — check `snap services smollm2`.
- The chat command works — send a prompt with `smollm2 chat` and confirm a sensible
  response is returned.
- If the model supports images, use `curl` against the running server to prompt it with
  an image and check the response makes sense.
- The response speed is reasonable given the shared GitHub Actions runner.
- Switch engines and models: confirm the correct components are downloaded, and if the
  engine is supported on the runner's hardware, confirm it works.

## Reporting

Only create a GitHub issue if you find a genuine problem (an error, a crash, a nonsensical
response, a missing component, or clearly unreasonable behaviour). If everything works,
do **not** create an issue — simply summarise your findings in the run log.

When you do create an issue, include:

1. **What went wrong** — a clear description of the failure and the check it belongs to.
2. **How to reproduce it** — the exact commands you ran, in order, including the channel
   (`${{ github.event.inputs.snap-channel }}`).
3. **Observed vs. expected behaviour** — command output, error messages, and what you
   expected instead.
4. **Environment details** — installed snap version and revision (`snap info smollm2`),
   runner architecture (`uname -m`), and the selected engine/model.
