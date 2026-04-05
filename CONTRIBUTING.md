# Contributing

Thanks for helping improve `agent-team-infra`.

This repository is a reusable multi-agent team configuration intended to be
copied into other projects. Contributions that make the setup clearer, more
useful, or easier to adopt are welcome.

## What to contribute

Good contributions include:

- fixes to agent behavior or instruction quality
- improved cross-tool consistency between Cursor, OpenCode, and Claude Code
- documentation clarifications and onboarding improvements
- new templates, examples, or guidance that help teams adapt the setup safely

If a change only makes sense for one tool, call that out clearly in the pull
request so reviewers can evaluate whether the divergence is intentional.

## Before you open a pull request

- Check whether an issue or discussion already covers the same idea.
- For larger changes, describe the motivation, expected behavior, and any
  tradeoffs before implementation.
- Keep changes focused. Separate unrelated improvements into different pull
  requests when possible.

## Making changes

When editing agent definitions or shared instructions:

- Keep Cursor, OpenCode, and Claude Code variants aligned when behavior should
  match across tools.
- Preserve intentional tool-specific differences and explain them in the pull
  request.
- Update [README.md](README.md) when contributor-facing behavior, setup steps,
  or supported workflows change.
- Avoid introducing references to tooling, CI, or validation steps that do not
  exist in this repository.

## Pull request checklist

Please include:

- a clear summary of what changed and why
- any relevant context, screenshots, or examples if the change affects usage
- documentation updates when setup or behavior changes
- confirmation that related agent/config variants were reviewed for consistency
- basic validation that the repository contents still make sense as a reusable
  template

## Review expectations

Reviews will focus on clarity, maintainability, and whether the repository
still works as a portable starting point for downstream projects. A small,
well-explained pull request is usually the fastest path to merge.
