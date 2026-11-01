# Preventing Persistent-State Pollution During Game Mode Transitions

[Article index](../README.md)

Switching between solo and cooperative play can accidentally mutate settings that belong to a different mode. The problem is not necessarily the mode switch itself; it is unclear ownership of temporary session state versus persistent user choices.

## Separate lifetimes

Persistent settings such as a selected map should not be overwritten by provisional lobby choices. A pending cooperative session can own its temporary selections until the user confirms entry. Canceling or leaving should discard that session state, not commit it into solo preferences.

## Test cancellation as a first-class path

Test enter, cancel, re-enter, change mode, and return to the original mode. Confirm that mission selection, map choice, and other user settings remain consistent. Avoid a broad state-management framework when a small explicit boundary between session and persistent state solves the actual issue.

The general rule: **temporary workflow state must not silently become durable user state.**
