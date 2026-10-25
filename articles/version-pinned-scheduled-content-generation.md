# Version-Pinned Scheduled Content Generation

[Article index](../README.md)

Scheduled content systems often generate items in batches over a period such as a season or week. If a configuration changes halfway through that period, simply reading the latest configuration on every run can create inconsistent batches or stop replenishment for content already in progress.

## Pin the generation policy at the period boundary

When a period begins, record which generation policy applies to that period. Follow-up batches should use the pinned policy unless the product explicitly supports a migration. The next period can use the newer policy without silently rewriting the meaning of already published content.

A minimal record may include:

- The period identifier and its start/end boundary.
- The generation-policy version or schedule identifier.
- The publication state and batch identifiers.
- The expected number of items and the result of validation.
- The reason for any skipped, replaced, or failed item.

The exact schema depends on the system. The important property is that a later configuration change does not ambiguously alter work that belongs to an earlier period.

## Distinguish safety substitution from successful generation

Replacing an item that cannot be generated can prevent a batch from being blocked, but it should not hide the underlying failure. Track generation failures, replacements, validation outcomes, and final publication separately. A result such as zero blocked slots in one test sample does not prove that every future input is valid.

Retries should be bounded, and repeated work should be idempotent where possible. A retry must know whether it is resuming the same batch or creating new content. Record failed attempts rather than deleting them from the evidence.

## Validate the period transition

Useful checks include:

1. Start a period with policy A and publish its first batch.
2. Change the configured policy to B.
3. Run replenishment for the active period and verify that it still uses A.
4. Start the next period and verify that it uses B.
5. Re-run the same scheduled job and check for duplicate or inconsistent publication.

These checks validate the version-boundary contract in the tested environment. They do not, by themselves, prove production scheduling reliability or correctness under every concurrent execution.

**The principle is simple: configuration may evolve, but already-started work needs an explicit and reproducible policy.**
