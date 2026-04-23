# Repository Guidelines

## Project Structure & Module Organization

This repository contains a Chinese translation of the Haskell MOOC plus runnable exercises. Course pages live in `docs/` and are ordered by lecture, for example `docs/01-and-so-it-begins.md`. MkDocs configuration is in `mkdocs.yml`, with CSS in `docs/stylesheets/extra.css` and assets in `docs/img/`. The root `img/` directory supports the README and source material. Haskell exercises are in `exercises/`; each `Set*.hs` file normally has a matching `Set*Test.hs`. Shared helpers are under `exercises/Mooc/`, examples under `exercises/Examples/`, and translation notes in `other/`.

## Build, Test, and Development Commands

- `mkdocs serve`: preview the translated course site locally.
- `mkdocs build`: build the static site into `site/`.
- `cd exercises && stack build`: install dependencies and compile the exercise environment.
- `cd exercises && stack runhaskell Set3aTest.hs`: run one exercise test file; replace the filename with the set you are working on.

The exercise project uses `exercises/stack.yaml` with resolver `lts-22.43` and GHC 9.6.6.

## Coding Style & Naming Conventions

Keep Markdown chapter filenames numbered and kebab-cased, matching `docs/NN-title.md`. Preserve Chinese terminology documented in `other/translation-guidance.md`. For Haskell, follow the surrounding exercise style: Haskell2010 syntax, two-space indentation where practical, explicit top-level type signatures, and names such as `Set10a.hs` and `Set10aTest.hs`. Avoid broad rewrites when fixing translation, test, or exercise issues.

## Testing Guidelines

Tests use Hspec and QuickCheck through the Stack project in `exercises/`. Run the nearest matching test file before submitting exercise changes, for example `stack runhaskell Set14bTest.hs`. When changing `exercises/Mooc/`, run several affected `Set*Test.hs` files or `stack build`. Keep new tests named after the exercise set they validate.

## Commit & Pull Request Guidelines

Recent commits use short summaries in Chinese or English, such as `翻译润色：第13-16讲术语一致性与可读性修正` or `lecture 6: reword, fix typo`. Keep commits focused on one topic. Pull requests should describe affected lectures or exercise sets, explain the reason, link issues when available, and list test/build commands run. Include screenshots only when visual output changes.

## Agent-Specific Instructions

Do not edit generated `site/` output. Change Markdown, CSS, MkDocs config, or exercise source files directly. Preserve the upstream course meaning; note translation ambiguity in the pull request or issue.
