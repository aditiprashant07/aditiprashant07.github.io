---
layout: page
permalink: /projects/kaizen-code/
---

# KaizenCode — Developer Analysis and Code Quality VS Code Extension

## 1. Project overview

KaizenCode is a Visual Studio Code extension project intended to bring code analysis and developer feedback into the editor. The repository combines a VS Code extension, a webview-based analysis interface, Python-side tooling, shared configuration, tests, and supporting database/API structure.

The current repository includes a TypeScript VS Code extension package with an Activity Bar view and an `Analysis & Stats` webview, plus a separate webview UI built with Vite. The project is structured to connect editor workflows with analysis services. The intended direction is to help developers inspect code quality, maintainability, and potential issues from a single workspace.

**Repository:** [Kaizen-Code](https://github.com/aditiprashant07/Kaizen-Code)

## 2. Objectives

- Build a VS Code-native interface for initiating code analysis.
- Present analysis results and useful statistics in an integrated sidebar/webview.
- Separate the editor extension, UI, backend/API, shared logic, and tests into maintainable components.
- Support a repeatable development workflow with type checks, linting, compilation, and tests.
- Create a foundation for static analysis and code-quality feedback that can scale to larger codebases.

## 3. Were the objectives achieved?

The repository establishes the extension and UI structure: the VS Code package contributes a KaizenCode Activity Bar container, an `Analysis & Stats` webview, and a `KaizenCode: Start Analysis` command. It also contains TypeScript build configuration, ESLint configuration, a Vite-based webview application, Python-related shared/API structure, and test directories.

The repository therefore demonstrates implementation of the editor-extension scaffold and supporting project architecture. The extension README is still the generated VS Code template and does not document completed analysis features or measured results. It would be inaccurate to claim that all planned static-analysis capabilities are fully implemented or validated based on the current public documentation alone.

## 4. Concepts learned and applied

- VS Code extension architecture, activation events, contributed commands, Activity Bar containers, and webview views.
- Extension-host and webview separation.
- Communication and UI composition across a TypeScript extension and a frontend webview.
- TypeScript project configuration, type checking, bundling, and build scripts.
- Vite-based frontend development.
- ESLint-based JavaScript/TypeScript code quality.
- Python project organization and shared modules.
- Separation of API, extension, shared logic, and UI responsibilities.
- Test scaffolding and development workflow automation.
- Designing developer tooling as an editor-integrated product rather than a standalone script.

## 5. Approach and what is different about it

### Approach

1. **Create the VS Code extension shell.** The extension package declares VS Code compatibility, activation after startup, an Activity Bar container, an analysis webview, and a command to start analysis.
2. **Separate the webview interface.** A dedicated Vite-based UI package provides the frontend surface independently from extension-host code.
3. **Organize supporting services.** The repository separates API/database-related code, shared constants, extension logic, and UI components into distinct directories.
4. **Establish engineering checks.** Package scripts include compilation, TypeScript checking, ESLint, test compilation, and VS Code extension test commands.
5. **Iterate toward analysis workflows.** The structure provides a place to connect static-analysis tools and display their findings in the VS Code interface.

### What makes this approach distinctive?

The main design choice is to make code analysis part of the developer's existing VS Code workflow. Instead of requiring developers to switch between separate command-line reports and dashboards, the extension provides an editor-side entry point and a dedicated analysis view. The separation between extension host, webview UI, and supporting services also creates a modular foundation for adding more analyzers over time.

This describes the repository's current structure and intended direction; the exact analyzer coverage and large-repository performance should be documented after those features are implemented and tested.

## 6. Technologies and tools used

| Technology | Role |
|---|---|
| Visual Studio Code Extension API | Editor integration, commands, Activity Bar, and webview |
| TypeScript | Extension implementation and type-safe frontend integration |
| JavaScript | Supporting extension/web tooling |
| Vite | Webview UI development and bundling |
| HTML and CSS | Webview presentation |
| Node.js | Extension build and development environment |
| pnpm | Package management in the extension workspace |
| esbuild | Extension bundling |
| ESLint | JavaScript/TypeScript linting |
| Python | Supporting API/shared analysis code structure |
| Pylint | Python style and static-check configuration |
| VS Code Test CLI / Mocha | Extension test tooling |

## Limitations and next steps

- Replace the generated extension README with user-facing documentation and screenshots.
- Document exactly which static-analysis tools are integrated and which findings are currently surfaced.
- Add end-to-end tests covering command invocation, analysis execution, and webview rendering.
- Record supported languages, repository-size limits, analysis time, and memory usage after benchmarking.
- Clearly distinguish implemented functionality from planned roadmap features.
