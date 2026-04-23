# IMPLEMENTATION_PHASE1.md

## Phase 1: Project Scaffolding and Testing Infrastructure

This blueprint details the technical execution for Phase 1, establishing the foundation for PXL Sweeper. It ensures a robust, testable, and maintainable codebase, ready for incremental feature delivery.

---

## 1. Architectural Design

**Data Structures & State:**
- No game logic or state is implemented in this phase.
- Directory structure and configuration files only.

**Key File Signatures:**
- `package.json`: Project metadata, scripts, and dependency declarations.
- `.gitignore`: Ignore node_modules, build artifacts, and editor files.
- `.prettierrc` or `prettier.config.js`: Prettier formatting rules.
- `.eslintrc.json` or `.eslintrc.cjs`: ESLint configuration.
- `public/index.html`: Minimal HTML5 boilerplate with script includes.
- `src/`: Source code root (empty for now).
- `test/`: Test code root (empty for now).

---

## 2. File-Level Strategy

| File/Folder           | Responsibility                                                      |
|----------------------|---------------------------------------------------------------------|
| `package.json`       | Define project, scripts, dependencies, and set `type: module`        |
| `.gitignore`         | Exclude node_modules, logs, build, and editor files                  |
| `.prettierrc`        | Code formatting rules (JSON or JS)                                   |
| `.eslintrc.json`     | Linting rules for JavaScript/ESM                                     |
| `public/index.html`  | HTML entry point, includes JS script (to be implemented)             |
| `src/`               | Source code root (empty placeholder)                                 |
| `test/`              | Test code root (empty placeholder)                                   |

---

## 3. Atomic Execution Steps (Plan-Act-Validate)

### 1. Set up `package.json` with test and lint scripts
- **Plan:**
  - Use `npm init` to scaffold.
  - Add `type: "module"`.
  - Add scripts: `test`, `lint`.
- **Act:**
  - Run `npm init -y`.
  - Edit `package.json` to add scripts and type.
- **Validate:**
  - Confirm `package.json` contains correct fields and scripts.

### 2. Install and configure Jest or Vitest
- **Plan:**
  - Choose Jest (default) or Vitest for ESM support.
- **Act:**
  - Run `npm install --save-dev jest` (or `vitest`).
  - Add test script: `jest` or `vitest`.
- **Validate:**
  - Run `npm test` (should pass, even with no tests).

### 3. Install and configure ESLint and Prettier
- **Plan:**
  - Install ESLint and Prettier as dev dependencies.
  - Scaffold config files.
- **Act:**
  - Run `npm install --save-dev eslint prettier`.
  - Run `npx eslint --init` (choose ESM, browser, style guide, JSON config).
  - Create `.prettierrc` with preferred rules.
- **Validate:**
  - Run `npm run lint` (should pass on empty src/).
  - Run `npx prettier --check .` (should pass).

### 4. Create directory structure (`src/`, `test/`, `public/`)
- **Plan:**
  - Create folders for source, tests, and public assets.
- **Act:**
  - `mkdir src test public`
- **Validate:**
  - Folders exist and are tracked by git (add `.gitkeep` if needed).

### 5. Create `index.html` stub with script includes
- **Plan:**
  - Minimal HTML5 boilerplate, placeholder for JS script.
- **Act:**
  - Create `public/index.html` with `<script src="../src/index.js"></script>` (to be implemented).
- **Validate:**
  - File exists, loads in browser (no errors expected).

### 6. Verify `npm test` and `npm run lint` run without errors
- **Plan:**
  - Run both commands after setup.
- **Act:**
  - Execute `npm test` and `npm run lint`.
- **Validate:**
  - Both commands complete successfully.

---

## 4. Edge Case & Boundary Audit
- ESM support: Ensure all configs and scripts use ESM-compatible syntax.
- Lint/test scripts: Should not fail on empty directories.
- `.gitignore`: Must not miss node_modules, .env, or editor files.
- Directory creation: Ensure folders are tracked (add `.gitkeep` if needed).
- `index.html`: Should not reference non-existent scripts.

---

## 5. Verification Protocol
- [ ] `npm install` completes without warnings
- [ ] `npm test` runs and produces a (passing) test report
- [ ] `npm run lint` runs and reports no errors
- [ ] `public/index.html` exists and loads without errors
- [ ] All config files present and valid
- [ ] Directory structure matches plan
- [ ] `.gitignore` excludes all necessary files

---

## 6. Code Scaffolding

### `package.json` (template)
```json
{
  "name": "pxl-sweeper",
  "version": "0.1.0",
  "type": "module",
  "scripts": {
    "test": "jest",
    "lint": "eslint ."
  },
  "devDependencies": {
    "jest": "^29.0.0",
    "eslint": "^8.0.0",
    "prettier": "^3.0.0"
  }
}
```

### `.gitignore` (template)
```
node_modules/
dist/
.env
.DS_Store
*.log
.idea/
.vscode/
```

### `.prettierrc` (template)
```
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2
}
```

### `.eslintrc.json` (template)
```
{
  "env": {
    "browser": true,
    "es2021": true
  },
  "extends": ["eslint:recommended"],
  "parserOptions": {
    "ecmaVersion": "latest",
    "sourceType": "module"
  },
  "rules": {}
}
```

### `public/index.html` (template)
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>PXL Sweeper</title>
</head>
<body>
  <div id="app"></div>
  <!-- JS entry will be added in later phases -->
</body>
</html>
```

---

> **Review this blueprint before implementation. All steps are atomic and verifiable. No game logic is introduced in this phase.
