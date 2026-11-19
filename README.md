# SmolLM2 inference snap
[![smollm2](https://snapcraft.io/smollm2/badge.svg)](https://snapcraft.io/smollm2)

SmolLM2 is a compact large language model (LLM) developed by Hugging Face, designed for efficient natural language tasks such as text generation, summarization, and question answering.

Use this snap to quickly install an optimized environment for local inference with SmolLM2.

The snap includes the following hardware-optimized inference engines:

* cpu: Optimized for x64 and ARM (armv8, armv9) CPUs
* intel-cpu: OpenVINO acceleration for Intel CPUs
* intel-gpu: OpenVINO acceleration for supported Intel GPUs
* nvidia-gpu: CUDA-enabled GPU acceleration

The most suitable engine is automatically selected based on the available hardware.

#### Install
```shell
sudo snap install smollm2
```

#### Run
```shell
smollm2
```

> [!TIP]
> Some accelerators require extra [drivers](https://documentation.ubuntu.com/inference-snaps/how-to/setup/drivers/) to be usable with this snap.

## Resources

📚 **[Documentation](https://documentation.ubuntu.com/inference-snaps/)**, learn how to use inference snaps

💬 **[Discussions](https://github.com/canonical/inference-snaps/discussions)**, ask questions and share ideas

🐛 **[Issues](https://github.com/canonical/inference-snaps/issues)**, report bugs and request features

## Build and install from source

Clone the repo:
```shell
git clone https://github.com/canonical/smollm2-snap
cd smollm2-snap
```

Initialize the development environment:
```shell
make init
```

Build and install snap:
```shell
make build
make install
```

## Renovate PR checks

Renovate PRs opt into lab testing with a standalone `lab-tests: true` line in
the latest commit message (set `commitBody` in the relevant Renovate package
rules). Tests run on PR creation, reopening, and updates.

The local `renovate.json` adds this marker and enables PR automerge for
llama.cpp and OpenVINO Model Server updates, without ignoring required checks.

The `Renovate Pr Checks` workflow skips lab tests and passes for non-Renovate
PRs and Renovate PRs without the marker. When the marker is present on a
Renovate PR, the `Renovate PR Checks` check requires successful lab tests.
Configure this check, `cla`,
and `lint` as required on `main` in a ruleset Renovate cannot bypass. Enable
repository auto-merge and configure Renovate PR automerge for the selected
dependencies separately; this workflow does not enable auto-merge itself.
