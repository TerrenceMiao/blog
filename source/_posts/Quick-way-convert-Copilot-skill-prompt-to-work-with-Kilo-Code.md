---
title: Quick way convert Copilot skill/prompt to work with Kilo Code
date: 2026-06-14 14:39:35
tags:
---

Kilo Code` doesn't natively read **.github-based** Copilot skill/prompt/workflow/convention files — that format is specific to `GitHub Copilot`'s custom instructions/prompts system.

```
$ tree .github
.github
├── conventions
│   ├── component-test-patterns.md
│   ├── test-anti-patterns.md
│   └── unit-test-conventions.md
├── prompts
│   ├── gen-it-tests.prompt.md
│   ├── gen-unit-tests.prompt.md
│   ├── it-coverage-gap-analysis.prompt.md
│   ├── unit-test-coverage-gap-analysis.prompt.md
│   └── uplift-unit-tests.prompt.md
└── skills
    ├── integration-test-coverage-analysis
    │   ├── integration-test-coverage-classifier.md
    │   ├── integration-test-dependency-extractor.md
    │   ├── integration-test-evidence-collector.md
    │   ├── integration-test-freshness-checker.md
    │   ├── integration-test-gap-report-writer.md
    │   ├── integration-test-gateway-scanner.md
    │   ├── integration-test-scope-validator.md
    │   ├── SKILL.md
    │   └── templates
    │       ├── Component-test-analysis.md
    │       └── IT-analysis.md
    ├── integration-test-generator
    │   ├── integration-test-code-generator.md
    │   ├── integration-test-gap-discoverer.md
    │   ├── integration-test-option-selector.md
    │   ├── integration-test-plan-builder.md
    │   └── SKILL.md
    ├── shared
    │   ├── diff-analysis-ci.md
    │   ├── diff-analysis-ide.md
    │   ├── test-quality-rules.md
    │   └── tiered-model-strategy.md
    ├── unit-test-coverage-analysis
    │   ├── SKILL.md
    │   ├── templates
    │   │   └── unit-test-coverage-analysis-report.md
    │   ├── unit-test-codebase-analysis.md
    │   ├── unit-test-coverage-analysis-preflight.md
    │   ├── unit-test-diff-scope-analysis.md
    │   ├── unit-test-project-discovery.md
    │   └── unit-test-report-synthesis.md
    └── unit-test-generator
        ├── SKILL.md
        ├── unit-test-code-generator.md
        ├── unit-test-diff-coverage-resolver.md
        ├── unit-test-infrastructure-setup.md
        ├── unit-test-post-generation-verification.md
        └── unit-test-refactor-specialist.md

11 directories, 40 files
```

A quick way to convert to `Kilo Code` custom way:

- Add `Kilo Code` directory structure

```
$ ln -s .github .kilo
```

- Convert prompt to `Kilo Code` custom slash command

```
$ cd .github

$ ln -s prompts commands
```

In `Kilo Code` chat window, then can find and run command e.g. `/gen-unit-tests`.
