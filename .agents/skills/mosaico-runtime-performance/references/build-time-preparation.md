# Build-time preparation and resource contracts

Read when expensive startup or repeated preparation computes data whose inputs
may already be fixed. Precomputation exchanges execution cost for stored data;
measure the affected stage before choosing it.

## Identify the actual invariant

| Inputs become known | Candidate treatment |
| --- | --- |
| Build configuration | Generate an immutable resource during the build |
| Selected profile or committed plan | Prepare off the realtime owner; cache by the complete input when reuse warrants it |
| Live input, playback position or device calibration | Keep the dependent work dynamic; isolate any constant subexpressions |

Enumerate every dependency: algorithm, seed, sample rate, profile, gain, geometry,
precision and metadata such as clip boundaries. Do not replace live recorded
music with a fixed example. Before removing an apparently redundant pass, inspect
whether its statistics, normalization or state affect later output.

## Keep generation tied to production behavior

Prefer running the existing portable production algorithm with a native build-host
compiler. Do not accidentally execute a target cross-compiled binary or maintain
an independent approximation solely for asset generation. Keep target SDK code
out of the portable generator.

Declare generator, source/header and configuration dependencies in the build.
Changes must regenerate the artifact, including a profile change in an existing
build directory. Keep generated binaries in build output and retain enough
metadata to identify inputs, representation, size and content hash. Verify the
resource embedded in the actual firmware, not just a neighboring output file.

For binary resources, define byte order, scalar representation, lengths, offsets
and schema explicitly. Validate profile, bounds and integrity before publication;
include numerical validity where applicable. Reject incompatible or damaged data
through the existing failure path. A silent slow fallback can conceal stale
assets and reintroduce the startup failure being investigated.

## Preserve ownership and count the whole cost

An immutable embedded resource and an allocated playback buffer have different
lifetimes. Copying into the existing owned representation is often the smallest
compatible change. Borrowing directly can save memory only if consumers remain
read-only, alignment/access constraints hold and destruction never frees the
embedded storage. Test failure cleanup and replacement while old data is live.

Count Flash growth, target rodata/XIP placement, owned copies, validation/loading
scratch and peak overlap with dynamic jobs. Embedded data may also occupy PSRAM;
eliminating synthesis does not automatically reduce steady memory use. Avoid
moving direct Flash reads onto a deadline owner without verifying cache-disabled
intervals and access behavior on the target.

## Verify both the result and the tradeoff

Compare generated samples/pixels and semantic metadata against the original
algorithm on identical inputs. Host agreement alone does not establish target
agreement: compiler, floating-point contraction and math-library differences can
matter. Use a bounded target comparison where appropriate, with the existing
error budget; do not loosen it merely to pass. Keep diagnostic regeneration out
of the final performance build.

Exercise corrupt/truncated data, profile mismatch, allocation failure and cleanup.
Measure loading/validation, total startup or interaction latency, memory margins
and deadline counters separately under comparable workloads. Recheck cold and
software starts and affected physical behavior. A shorter preparation stage is
not the same measurement as a proportionally faster complete boot.

Historical motivation: Echoy moved fixed cues and audition pools to build-time
generation while retaining dynamic recording synthesis. Its measured sound
preparation fell from about 3.42 s to 0.132–0.148 s at a cost of roughly 1 MB each
in firmware size and steady PSRAM. This is one application's result, not a target,
expected speedup, memory budget or device authorization for another project.
