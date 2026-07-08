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

permissions:
  contents: read

network:
  allowed:
    - defaults
    - "*.snapcraft.io"
    - "*.snapcraftcontent.com"
    - "*.canonical.com"
    - "*.ubuntu.com"
    - "documentation.ubuntu.com"

tools:
  bash: [":*"]
  web-fetch:

timeout-minutes: 30

safe-outputs:
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

Install the snap from the requested channel:

```
sudo snap install smollm2 --channel="${{ github.event.inputs.snap-channel }}"
```

If the channel string already contains a track/risk/branch, pass it exactly as given.

## Things to check

- The snap installs correctly.
- An engine is automatically selected.
- The server daemon is running (check `snap services smollm2`).
- The chat command works — send a prompt with `smollm2 chat` and confirm a sensible response.
- If the model supports images, use `curl` to prompt it with an image and check the response makes sense.
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
