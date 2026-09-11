# vita-agent-model test acceptance

Refs #1; parent VitaDAO/vita-agent-model-tinfoil-config#3. The canonical issues
record each test release and its evidence; this file describes the current
candidate and acceptance procedure.

## Exact serving identity

- Model-serving repository source: `VitaDAO/vita-agent-model-tinfoil-config@9ebdc612db3fd351457dbe96991bf9e331f31ace`.
- Upstream SGLang source: `sgl-project/sglang@db272201a2dbd72e5699e443240a851f1313ad45`.
- Image: `ghcr.io/vitadao/vita-agent-model-sglang@sha256:47403a0af08f629a55fd65695bb250f378e9938c358b795ae50c1c38ad9cfe08`.

The model-serving source builds from the exact September 9 SGLang image. It
changes the XGrammar compiler and Jinja output-contract context; it does not
replace SGLang's server-argument or reasoning-budget implementation. The model
repository commit is not an upstream SGLang commit.
[Build 34636989159](https://github.com/VitaDAO/vita-agent-model-tinfoil-config/actions/runs/34636989159)
passed Linux native and installed compiler/parser/renderer checks and published
this image. Those checks are distinct from live GPU acceptance.

## Current controlled experiment

Test release v0.5.94 used this image with the original unlimited-reasoning
settings. It passed 29/30 original A–F checks: one long JSON response exhausted
7,000 tokens in reasoning with no final answer. A supplemental test with complete
synthetic source contents also exhausted that budget in one long tool response
(9/10 passed). Completed outputs conformed to the original contract. One original
tool answer exceeded the prompt's block count, so structural conformance alone
is not an instruction-following or clinical-quality claim.

The next candidate adds only `--enable-strict-thinking` and
`SGLANG_MAX_THINK_TOKENS='4096'`. This is an intentional reasoning-budget change,
not an image-only comparison. Thinking remains enabled; after the native budget,
SGLang forces the normal reasoning-end token and continues with the output
grammar. The 7,000-token test allowance then leaves about 2,900 tokens for the
answer. Shorter total allowances and tasks needing more reasoning still require
appropriate caller budgets. The upstream `custom_params.thinking_budget`
per-request override remains available and must be verified live.

The implementation is already present in pinned SGLang:
[reasoning backend](https://github.com/sgl-project/sglang/blob/db272201a2dbd72e5699e443240a851f1313ad45/python/sglang/srt/constrained/reasoner_grammar_backend.py),
[request budget](https://github.com/sgl-project/sglang/blob/db272201a2dbd72e5699e443240a851f1313ad45/python/sglang/srt/managers/grammar_manager.py),
[environment default](https://github.com/sgl-project/sglang/blob/db272201a2dbd72e5699e443240a851f1313ad45/python/sglang/srt/environ.py).
The default environment budget is unlimited; filtering requires the strict
thinking flag. This test does not add a custom logit processor or repair output.

The image, Fable FP8 target, DFlash2 draft, eight draft tokens, sampling requests,
H200, eight CPUs and 128 GiB memory stay fixed. A hard reasoning limit can affect
answer quality and runtime performance; neither is assumed preserved.

## Acceptance and lifecycle

1. Verify attestation, health, model identity and the selected test tag.
2. Run the original A–F probe five times each, `max_tokens=7000`, without a
   client thinking override. Require 30/30; preserve failures and raw synthetic
   responses. Check reasoning-token counts and explicit small per-request
   budgets to prove native enforcement.
3. Inspect long replies with complete synthetic records and source contents,
   including factual values, requested structure and inference limitations.
   These supplement the original probe and never replace its pass requirement.
4. Verify streaming tool calls and simultaneous requests with different
   contracts. No partial-JSON repair.
5. Run the same five-pass, 2,000-token single-stream benchmark. Baseline v0.10.0
   median was 162.2 tokens/sec; v0.5.94 was 161.7. Require at least 145.98
   tokens/sec, and report first-token latency separately. Report the intentional
   reasoning-policy difference alongside performance results.

Use only test enclave `1a482a8d-c0d4-4896-b323-c20a9779663d`, keeping automatic
updates disabled, confidential computing enabled, debug disabled and no SSH or
user-data secrets. A tag/attestation does not prove startup or acceptance.
Production `vita-agent-model` remains stopped by the principal's latest request;
do not automatically restore v0.10.0. On test startup failure, stop the test and
preserve v0.5.94 as its prior attested configuration. Promote only the configuration
and image that pass the recorded gates through the separate model repository.
