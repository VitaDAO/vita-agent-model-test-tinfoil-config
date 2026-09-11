# Isolated Fable compiler acceptance

Refs #1; parent VitaDAO/vita-agent-model-tinfoil-config#3.

Candidate serving source: 202db8d8eda215a0c48b9153d04ae9b8f4871b74. Image: ghcr.io/vitadao/vita-agent-model-sglang@sha256:6bac34322a6974d03b93c2ff8fa0cdc96510b4a7c31df78334983c2244ef8ae2. Current parent head 05b02bb has identical docker/scripts/tests/workflow runtime inputs, with an explicit documented reference-support boundary.

The configuration is copied from the live model configuration at 6f89b7e3a4d77f1814467b8c97c37a98adf3b8a0. Only the serving image differs: same target/draft weights, DFlash, sampling flags, H200, 8 CPUs and 128GiB memory. No production container uses this test repository in the checked inventory.

GitHub run 34617502325 completed its native job and image build/installed-test/publication step successfully; cancellation occurred afterward during cleanup. The image manifest is independently present in GHCR. This is not a GPU acceptance claim.

Create only vita-agent-model-schema-test from this repository's attested tag. Keep auto-update disabled, confidential computing enabled, no debug keys or user-data secrets. Use synthetic A–F checks five times, default thinking, and the same single-stream benchmark as live v0.10.0 (median 162.2 tokens/s). Require speed within 10 percent and schema correctness before parent production promotion. Stop the test enclave after completion/failure.
