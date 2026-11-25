# Анализ структуры проекта

**Дата генерации:** 5/28/2026, 4:04:10 PM
**Обработано файлов:** 32
**Всего элементов (с учётом папок):** 36

## Структура проекта

```
├── bin/
│   ├── gitcracken-about.ts
│   ├── gitcracken-appid-generate.ts
│   ├── gitcracken-appid-read.ts
│   ├── gitcracken-appid.ts
│   ├── gitcracken-patcher.ts
│   ├── gitcracken-secfile.ts
│   └── gitcracken.ts
├── patches/
│   ├── development.diff
│   ├── individual.diff
│   ├── pro.diff
│   ├── selfhosted.diff
│   ├── staging.diff
│   └── standalone.diff
├── src/
│   ├── appId.ts
│   ├── index.ts
│   ├── logo.ts
│   ├── patcher.ts
│   ├── platform.ts
│   └── secFile.ts
├── types/
│   ├── asar.d.ts
│   ├── getmac.d.ts
│   └── index.d.ts
├── CHANGELOG.md
├── LICENSE
├── README.md
├── global.ts
├── index.ts
├── jest.config.js
├── package.json
├── parser.js
├── tsconfig.json
└── tslint.json
```

## Оглавление

- [bin/gitcracken-about.ts](#bin-gitcracken-about_ts)
- [bin/gitcracken-appid-generate.ts](#bin-gitcracken-appid-generate_ts)
- [bin/gitcracken-appid-read.ts](#bin-gitcracken-appid-read_ts)
- [bin/gitcracken-appid.ts](#bin-gitcracken-appid_ts)
- [bin/gitcracken-patcher.ts](#bin-gitcracken-patcher_ts)
- [bin/gitcracken-secfile.ts](#bin-gitcracken-secfile_ts)
- [bin/gitcracken.ts](#bin-gitcracken_ts)
- [CHANGELOG.md](#CHANGELOG_md)
- [global.ts](#global_ts)
- [index.ts](#index_ts)
- [jest.config.js](#jest_config_js)
- [LICENSE](#LICENSE)
- [package.json](#package_json)
- [parser.js](#parser_js)
- [patches/development.diff](#patches-development_diff)
- [patches/individual.diff](#patches-individual_diff)
- [patches/pro.diff](#patches-pro_diff)
- [patches/selfhosted.diff](#patches-selfhosted_diff)
- [patches/staging.diff](#patches-staging_diff)
- [patches/standalone.diff](#patches-standalone_diff)
- [README.md](#README_md)
- [src/appId.ts](#src-appId_ts)
- [src/index.ts](#src-index_ts)
- [src/logo.ts](#src-logo_ts)
- [src/patcher.ts](#src-patcher_ts)
- [src/platform.ts](#src-platform_ts)
- [src/secFile.ts](#src-secFile_ts)
- [tsconfig.json](#tsconfig_json)
- [tslint.json](#tslint_json)
- [types/asar.d.ts](#types-asar_d_ts)
- [types/getmac.d.ts](#types-getmac_d_ts)
- [types/index.d.ts](#types-index_d_ts)

## Содержимое файлов

// bin/gitcracken-about.ts

```typescript
import * as program from "commander";

import {Logo} from "../";

program
  .name("gitcracken-about")
  .description("about GitCracken")
  .action(() => {
    Logo.print();
  })
  .parse(process.argv);

```

// bin/gitcracken-appid-generate.ts

```typescript
import chalk from "chalk";
import * as program from "commander";

import {AppId, Logo} from "../";

program
  .name("gitcracken-appid-generate")
  .description("generate GitKraken AppId")
  .option("-m, --mac <value>", "use specific mac address (or any other string)")
  .action(async () => {
    Logo.print();
    console.log(
      `${chalk.green("==>")} Generated AppId ${chalk.green(
        program.mac ? AppId.generate(program.mac) : await AppId.generateAsync(),
      )}`,
    );
  })
  .parse(process.argv);

```

// bin/gitcracken-appid-read.ts

```typescript
import chalk from "chalk";
import * as program from "commander";

import {AppId, Logo} from "../src";

program
  .name("gitcracken-appid-read")
  .description("read GitKraken AppId from config")
  .option("-c, --config <file>", "path to config")
  .action(() => {
    Logo.print();
    console.log(
      `${chalk.green("==>")} Current AppId ${chalk.green(
        AppId.read(program.config) || "null",
      )}`,
    );
  })
  .parse(process.argv);

```

// bin/gitcracken-appid.ts

```typescript
import * as program from "commander";

program
  .name("gitcracken-appid")
  .description("GitKraken AppId")
  .command("generate", "generate GitKraken AppId")
  .command("read", "read GitKraken AppId from config")
  .parse(process.argv);

```

// bin/gitcracken-patcher.ts

```typescript
import chalk from "chalk";
import * as program from "commander";
import * as emoji from "node-emoji";

import {Logo, Patcher} from "../";

const enum Actions {
  backup = 1,
  unpack = 2,
  patch = 3,
  pack = 4,
  remove = 5,
}

async function executeActions(actions: Actions[]) {
  Logo.print();
  const patcher = new Patcher({
    asar: program.asar,
    dir: program.dir,
    features: program.feature,
  });
  for (const action of actions) {
    switch (action) {
      case Actions.backup: {
        console.log(
          `${chalk.green("==>")} ${emoji.get("package")} Backup ` +
            `${chalk.green(patcher.asar)} ➔ ${chalk.green(
              patcher.backupAsar(),
            )}`,
        );
        break;
      }
      case Actions.unpack: {
        console.log(
          `${chalk.green("==>")} ${emoji.get("unlock")} Unpack ${chalk.green(
            patcher.asar,
          )} ➔ ` + `${chalk.green(patcher.dir)}`,
        );
        patcher.unpackAsar();
        break;
      }
      case Actions.patch: {
        console.log(
          `${chalk.green("==>")} ${emoji.get("hammer")} Patch ${chalk.green(
            patcher.dir,
          )} ` +
            `with ${patcher.features
              .map((feature) => `${chalk.green(feature)}`)
              .join(", ")} features`,
        );
        patcher.patchDir();
        break;
      }
      case Actions.pack: {
        console.log(
          `${chalk.green("==>")} ${emoji.get("lock")} Pack ${chalk.green(
            patcher.dir,
          )} ➔ ` + `${chalk.green(patcher.asar)}`,
        );
        await patcher.packDirAsync();
        break;
      }
      case Actions.remove: {
        console.log(
          `${chalk.green("==>")} ${emoji.get("fire")} Remove ${chalk.green(
            patcher.dir,
          )}`,
        );
        patcher.removeDir();
        break;
      }
    }
  }
  console.log(`${chalk.green("==>")} ${emoji.get("ok_hand")} Patching done!`);
}

program
  .name("gitcracken-patcher")
  .description("GitKraken patcher")
  .option("-a, --asar <file>", "app.asar file")
  .option("-d, --dir <dir>", "app directory")
  .option(
    "-f, --feature <value>",
    "patcher feature",
    (val, memo) => {
      memo.push(val);
      return memo;
    },
    [],
  )
  .arguments("[actions...]")
  .action(async (strActions?: string[]) => {
    if (program.feature.length === 0) {
      program.feature.push("pro");
    }
    const actions: Actions[] = [];
    if (!strActions || !strActions.length) {
      actions.push(
        Actions.backup,
        Actions.unpack,
        Actions.patch,
        Actions.pack,
        Actions.remove,
      );
    } else {
      strActions.forEach((item) => {
        switch (item.toLowerCase()) {
          case "backup":
            actions.push(Actions.backup);
            break;
          case "unpack":
            actions.push(Actions.unpack);
            break;
          case "patch":
            actions.push(Actions.patch);
            break;
          case "pack":
            actions.push(Actions.pack);
            break;
          case "remove":
            actions.push(Actions.remove);
            break;
        }
      });
    }
    await executeActions(actions);
  })
  .parse(process.argv);

```

// bin/gitcracken-secfile.ts

```typescript
import chalk from "chalk";
import * as program from "commander";

import {AppId, Logo, SecFile} from "../";

program
  .name("gitcracken-secfile")
  .description("read GitKraken secFile")
  .option("-i, --appid <id>", "AppId for secFile decrypt", AppId.read())
  .arguments("[files...]")
  .action((files?: string[]) => {
    Logo.print();
    for (const file of files || []) {
      console.log(`${chalk.green("==>")} ${chalk.bold(file)}`);
      const secFile = new SecFile(file, program.appid);
      secFile.read();
      console.log(JSON.stringify(secFile.data, null, 2));
    }
  })
  .parse(process.argv);

```

// bin/gitcracken.ts

```typescript
#!/usr/bin/env node

import * as program from "commander";

import {packageJson} from "../global";

program
  .name("gitcracken")
  .version(packageJson.version)
  .description(packageJson.description)
  .command("about", "about GitCracken")
  .command("appid", "GitKraken AppId")
  .command("patcher [actions...]", "GitKraken patcher")
  .command("secfile [files...]", "read GitKraken secFile")
  .parse(process.argv);

```

// CHANGELOG.md

```markdown
# v0.6.2 - Patches with Love :cupid:

- Support GitKraken v6.5.2
  - Pro
  - Individual
  - Enterprise
    - Self-Hosted
    - Stand-Alone
  - Modes
    - Development
    - Staging
- Bump dependencies

> Our greatest glory is not in never falling, but in getting up every time we do.

# v0.6.1 - Return of the missing patches

- Support GitKraken v6.0.0
  - Pro
  - Individual
  - Enterprise
    - Self-Hosted
    - Stand-Alone
  - Modes
    - Development
    - Staging
- Bump dependencies

# v0.6.0 - Lightspeed :rocket:

- Support GitKraken Pro v6.0.0
- Bump dependencies

```

// global.ts

```typescript
/// <reference path="./types/index.d.ts" />

import * as path from "path";

import * as fs from "fs-extra";
import * as pkgDir from "pkg-dir";

/**
 * Base directory with package.json
 */
export const baseDir = pkgDir.sync(__dirname) as string;

/**
 * package.json
 */
export const packageJson: {
  author: string;
  description: string;
  homepage: string;
  license: string;
  name: string;
  version: string;
} = fs.readJSONSync(path.join(baseDir, "package.json"));

```

// index.ts

```typescript
export * from "./src";

```

// jest.config.js

```javascript
const { createDefaultPreset } = require("ts-jest");

const tsJestTransformCfg = createDefaultPreset().transform;

console.log('ts-jest', JSON.stringify(tsJestTransformCfg, null, 2))

/** @type {import("jest").Config} **/
module.exports = {
  testEnvironment: "node",
  transform: {
    ...tsJestTransformCfg,
  },
};
```

// LICENSE (base64, size: 1074, mtime: 2025-04-01T11:00:32.000Z, md5: 1efa27513cf6f09226668ee2e3d4d1ce)

```
TUlUIExpY2Vuc2UKCkNvcHlyaWdodCAoYykgMjAxOC0yMDIwIEtpbGxXb2xmVmxhZAoKUGVybWlzc2lvbiBpcyBoZXJlYnkgZ3JhbnRlZCwgZnJlZSBvZiBjaGFyZ2UsIHRvIGFueSBwZXJzb24gb2J0YWluaW5nIGEgY29weQpvZiB0aGlzIHNvZnR3YXJlIGFuZCBhc3NvY2lhdGVkIGRvY3VtZW50YXRpb24gZmlsZXMgKHRoZSAiU29mdHdhcmUiKSwgdG8gZGVhbAppbiB0aGUgU29mdHdhcmUgd2l0aG91dCByZXN0cmljdGlvbiwgaW5jbHVkaW5nIHdpdGhvdXQgbGltaXRhdGlvbiB0aGUgcmlnaHRzCnRvIHVzZSwgY29weSwgbW9kaWZ5LCBtZXJnZSwgcHVibGlzaCwgZGlzdHJpYnV0ZSwgc3VibGljZW5zZSwgYW5kL29yIHNlbGwKY29waWVzIG9mIHRoZSBTb2Z0d2FyZSwgYW5kIHRvIHBlcm1pdCBwZXJzb25zIHRvIHdob20gdGhlIFNvZnR3YXJlIGlzCmZ1cm5pc2hlZCB0byBkbyBzbywgc3ViamVjdCB0byB0aGUgZm9sbG93aW5nIGNvbmRpdGlvbnM6CgpUaGUgYWJvdmUgY29weXJpZ2h0IG5vdGljZSBhbmQgdGhpcyBwZXJtaXNzaW9uIG5vdGljZSBzaGFsbCBiZSBpbmNsdWRlZCBpbiBhbGwKY29waWVzIG9yIHN1YnN0YW50aWFsIHBvcnRpb25zIG9mIHRoZSBTb2Z0d2FyZS4KClRIRSBTT0ZUV0FSRSBJUyBQUk9WSURFRCAiQVMgSVMiLCBXSVRIT1VUIFdBUlJBTlRZIE9GIEFOWSBLSU5ELCBFWFBSRVNTIE9SCklNUExJRUQsIElOQ0xVRElORyBCVVQgTk9UIExJTUlURUQgVE8gVEhFIFdBUlJBTlRJRVMgT0YgTUVSQ0hBTlRBQklMSVRZLApGSVRORVNTIEZPUiBBIFBBUlRJQ1VMQVIgUFVSUE9TRSBBTkQgTk9OSU5GUklOR0VNRU5ULiBJTiBOTyBFVkVOVCBTSEFMTCBUSEUKQVVUSE9SUyBPUiBDT1BZUklHSFQgSE9MREVSUyBCRSBMSUFCTEUgRk9SIEFOWSBDTEFJTSwgREFNQUdFUyBPUiBPVEhFUgpMSUFCSUxJVFksIFdIRVRIRVIgSU4gQU4gQUNUSU9OIE9GIENPTlRSQUNULCBUT1JUIE9SIE9USEVSV0lTRSwgQVJJU0lORyBGUk9NLApPVVQgT0YgT1IgSU4gQ09OTkVDVElPTiBXSVRIIFRIRSBTT0ZUV0FSRSBPUiBUSEUgVVNFIE9SIE9USEVSIERFQUxJTkdTIElOIFRIRQpTT0ZUV0FSRS4K
```

// package.json

```json
{
  "name": "gitcracken",
  "description": "GitKraken utils for non-commercial use",
  "author": "PMExtra, KillWolfVlad",
  "version": "8.4.0",
  "license": "MIT",
  "private": true,
  "homepage": "https://blog.jubeat.net/",
  "main": "dist/index.js",
  "bin": {
    "gitcracken": "dist/bin/gitcracken.js"
  },
  "scripts": {
    "build": "rimraf dist && tsc",
    "gitcracken": "node dist/bin/gitcracken.js",
    "gitcracken:dev": "node $NODE_DEBUG_OPTION -r ts-node/register bin/gitcracken.ts",
    "lint": "tslint -p tsconfig.json"
  },
  "dependencies": {
    "asar": "^2.0.1",
    "chalk": "^2.4.2",
    "commander": "^2.20.0",
    "diff": "^4.0.1",
    "figlet": "^1.2.3",
    "fs-extra": "^8.1.0",
    "getmac": "^1.4.6",
    "natsort": "^2.0.2",
    "node-emoji": "^1.10.0",
    "pkg-dir": "^4.2.0",
    "uuid": "^3.3.2"
  },
  "devDependencies": {
    "@types/diff": "^4.0.2",
    "@types/figlet": "^1.2.0",
    "@types/fs-extra": "^8.0.0",
    "@types/node": "^12.0.10",
    "@types/node-emoji": "^1.8.1",
    "@types/uuid": "^3.4.4",
    "prettier": "^1.18.2",
    "rimraf": "^2.6.3",
    "ts-node": "^8.3.0",
    "tslint": "^5.18.0",
    "tslint-config-prettier": "^1.18.0",
    "tslint-plugin-prettier": "^2.0.1",
    "typescript": "^3.5.2"
  },
  "files": [
    "dist",
    "patches"
  ]
}

```

// parser.js

```javascript
import { promises as fs, createReadStream } from "fs";
import path from "path";
import crypto from "crypto";

// ----------------------------------------------------------------------
// Фиксированные настройки (без параметров командной строки)
// ----------------------------------------------------------------------

// Игнорируемые директории (на любом уровне)
const IGNORE_DIRS = new Set([
  "node_modules",
  ".git",
  ".vscode",
  ".idea",
  "dist",
  "build",
  "coverage",
  ".next",
  "out",
  "vendor",
  "tmp",
  "temp",
  "logs",
  ".gitlab",
  ".helm",
  ".husky",
  ".nx",
]);

// Игнорируемые файлы по имени (на любом уровне)
const IGNORE_FILES = new Set([
  "result.md",
  ".DS_Store",
  "thumbs.db",
  ".env",
  ".gitignore",
  "package-lock.json",
  "yarn.lock",
  "pnpm-lock.yaml",
  "composer.lock",
  "game.db",
]);

// Максимальный размер бинарного файла для включения base64 (1 МБ)
const MAX_BINARY_SIZE = 1 * 1024 * 1024;

// ----------------------------------------------------------------------
// Определение типов файлов и языков (с группировкой для читаемости)
// ----------------------------------------------------------------------

const TEXT_EXTENSIONS = new Set([
  // -------------------------------
  // Web
  // -------------------------------
  ".html",
  ".htm",
  ".css",
  ".scss",
  ".sass",
  ".less",
  ".js",
  ".jsx",
  ".ts",
  ".tsx",
  ".vue",
  ".svelte",

  // -------------------------------
  // Backend
  // -------------------------------
  ".py",
  ".rb",
  ".php",
  ".java",
  ".go",
  ".rs",
  ".swift",
  ".kt",
  ".scala",

  // -------------------------------
  // Config / data
  // -------------------------------
  ".json",
  ".yaml",
  ".yml",
  ".toml",
  ".ini",
  ".cfg",
  ".conf",
  ".xml",
  ".svg",
  ".properties",
  ".env.example",

  // -------------------------------
  // Documentation
  // -------------------------------
  ".md",
  ".rst",
  ".txt",
  ".tex",
  ".log",
  ".csv",
  ".tsv",
  ".psv",

  // -------------------------------
  // Shell / scripts
  // -------------------------------
  ".sh",
  ".bash",
  ".zsh",
  ".fish",
  ".ps1",
  ".bat",
  ".cmd",

  // -------------------------------
  // Database
  // -------------------------------
  ".sql",
  ".graphql",
  ".gql",

  // -------------------------------
  // Other code
  // -------------------------------
  ".cpp",
  ".c",
  ".h",
  ".hpp",
  ".cs",
  ".fs",
  ".fsx",
  ".lua",
  ".pl",
  ".pm",

  // -------------------------------
  // Templates
  // -------------------------------
  ".ejs",
  ".pug",
  ".jade",
  ".handlebars",
  ".hbs",
  ".mustache",
]);

const EXTENSION_TO_LANG = {
  // Web
  ".js": "javascript",
  ".jsx": "jsx",
  ".ts": "typescript",
  ".tsx": "tsx",
  ".vue": "vue",
  ".svelte": "svelte",
  ".html": "html",
  ".htm": "html",
  ".css": "css",
  ".scss": "scss",
  ".sass": "sass",
  ".less": "less",

  // Backend
  ".py": "python",
  ".rb": "ruby",
  ".php": "php",
  ".java": "java",
  ".go": "go",
  ".rs": "rust",
  ".swift": "swift",
  ".kt": "kotlin",
  ".scala": "scala",

  // Config / data
  ".json": "json",
  ".yaml": "yaml",
  ".yml": "yaml",
  ".toml": "toml",
  ".xml": "xml",
  ".svg": "svg",
  ".properties": "properties",
  ".ini": "ini",
  ".cfg": "ini",
  ".conf": "ini",

  // Documentation
  ".md": "markdown",
  ".rst": "rst",
  ".txt": "text",
  ".log": "text",
  ".csv": "csv",
  ".tsv": "tsv",

  // Shell / scripts
  ".sh": "bash",
  ".bash": "bash",
  ".zsh": "bash",
  ".ps1": "powershell",
  ".bat": "batch",
  ".cmd": "batch",

  // Database
  ".sql": "sql",
  ".graphql": "graphql",
  ".gql": "graphql",

  // Other code
  ".cpp": "cpp",
  ".c": "c",
  ".h": "c",
  ".hpp": "cpp",
  ".cs": "csharp",
  ".lua": "lua",
  ".pl": "perl",
  ".pm": "perl",

  // Templates
  ".ejs": "ejs",
};

function isTextFile(filePath) {
  const ext = path.extname(filePath).toLowerCase();
  return TEXT_EXTENSIONS.has(ext);
}

function getLanguage(filePath) {
  const ext = path.extname(filePath).toLowerCase();
  return EXTENSION_TO_LANG[ext] || "";
}

// ----------------------------------------------------------------------
// Обход директорий – собираем ВСЕ неигнорируемые пути (и папки, и файлы)
// ----------------------------------------------------------------------

async function walk(dir, baseDir = dir, results = []) {
  const entries = await fs.readdir(dir, { withFileTypes: true });

  for (const entry of entries) {
    const fullPath = path.join(dir, entry.name);

    if (IGNORE_FILES.has(entry.name)) continue;

    if (entry.isDirectory()) {
      if (IGNORE_DIRS.has(entry.name)) continue;
      // добавляем папку в результаты (даже если она пуста)
      results.push({ path: fullPath, isDirectory: true });
      await walk(fullPath, baseDir, results);
    } else {
      results.push({ path: fullPath, isDirectory: false });
    }
  }
  return results;
}

// ----------------------------------------------------------------------
// Чтение файла с обработкой
// ----------------------------------------------------------------------

async function readFileContent(filePath) {
  const stats = await fs.stat(filePath);
  const isText = isTextFile(filePath);

  if (isText) {
    try {
      const content = await fs.readFile(filePath, "utf8");
      return { type: "text", content, stats };
    } catch (err) {
      console.warn(
        `Не удалось прочитать текстовый файл ${filePath}: ${err.message}`,
      );
      return null;
    }
  }

  // Бинарный файл
  if (stats.size > MAX_BINARY_SIZE) {
    // слишком большой – только метаданные
    const hash = crypto
      .createHash("md5")
      .update(await fs.readFile(filePath))
      .digest("hex");
    return { type: "binary-meta", stats, hash };
  } else {
    const buffer = await fs.readFile(filePath);
    const base64 = buffer.toString("base64");
    const hash = crypto.createHash("md5").update(buffer).digest("hex");
    return { type: "binary", content: base64, stats, hash };
  }
}

// ----------------------------------------------------------------------
// Генерация дерева каталогов
// ----------------------------------------------------------------------

function generateTree(entries, baseDir) {
  const root = { name: path.basename(baseDir), children: {}, files: [] };

  for (const entry of entries) {
    const relative = path.relative(baseDir, entry.path);
    const parts = relative.split(path.sep);
    let current = root;

    for (let i = 0; i < parts.length; i++) {
      const part = parts[i];
      const isLast = i === parts.length - 1;

      if (!isLast) {
        if (!current.children[part]) {
          current.children[part] = { name: part, children: {}, files: [] };
        }
        current = current.children[part];
      } else {
        if (entry.isDirectory) {
          if (!current.children[part]) {
            current.children[part] = { name: part, children: {}, files: [] };
          }
        } else {
          current.files.push(part);
        }
      }
    }
  }

  // рекурсивная отрисовка
  function render(node, prefix = "") {
    const lines = [];
    const dirs = Object.keys(node.children).sort();
    const files = node.files.sort();

    for (let i = 0; i < dirs.length; i++) {
      const dirName = dirs[i];
      const child = node.children[dirName];
      const isLastDir = i === dirs.length - 1 && files.length === 0;
      lines.push(prefix + (isLastDir ? "└── " : "├── ") + dirName + "/");
      lines.push(
        ...render(child, prefix + (isLastDir ? "    " : "│   "), isLastDir),
      );
    }

    for (let i = 0; i < files.length; i++) {
      const fileName = files[i];
      const isLastFile = i === files.length - 1;
      lines.push(prefix + (isLastFile ? "└── " : "├── ") + fileName);
    }
    return lines;
  }

  return render(root).join("\n");
}

// ----------------------------------------------------------------------
// Основная функция
// ----------------------------------------------------------------------

async function main() {
  const startDir = process.cwd();
  console.log(`Сканирование директории: ${startDir}`);

  const entries = await walk(startDir);
  entries.sort((a, b) => a.path.localeCompare(b.path));

  const filePaths = entries.filter((e) => !e.isDirectory).map((e) => e.path);
  console.log(
    `Найдено элементов: всего ${entries.length}, файлов: ${filePaths.length}`,
  );

  const tree = generateTree(entries, startDir);

  const fileBlocks = [];
  const tocItems = [];

  let totalFiles = filePaths.length;
  let parsedFiles = 0;
  let skippedBinaryLarge = 0;
  let failedFiles = 0;

  for (const filePath of filePaths) {
    const relativePath = path.relative(startDir, filePath);

    let fileData;
    try {
      fileData = await readFileContent(filePath);
    } catch (err) {
      console.warn(`Ошибка обработки ${filePath}: ${err.message}`);
      failedFiles++;
      continue;
    }

    if (!fileData) {
      failedFiles++;
      continue;
    }

    if (fileData.type === "text" || fileData.type === "binary") {
      parsedFiles++;
    } else if (fileData.type === "binary-meta") {
      skippedBinaryLarge++;
    }

    const anchor = relativePath
      .replace(/[^a-zA-Z0-9\-_/]/g, "_")
      .replace(/\//g, "-");
    tocItems.push(`- [${relativePath}](#${anchor})`);

    let block = `// ${relativePath}`;

    if (fileData.type === "text") {
      const lang = getLanguage(filePath);
      block += `\n\n\`\`\`${lang}\n${fileData.content}\n\`\`\``;
    } else if (fileData.type === "binary") {
      block += ` (base64, size: ${fileData.stats.size}, mtime: ${fileData.stats.mtime.toISOString()}, md5: ${fileData.hash})\n\n\`\`\`\n${fileData.content}\n\`\`\``;
    } else {
      block += ` (meta only, size: ${fileData.stats.size}, mtime: ${fileData.stats.mtime.toISOString()}, md5: ${fileData.hash})\n\n\`\`\`\`\`\``;
    }

    fileBlocks.push(block + "\n\n");
  }

  const header = `# Анализ структуры проекта

**Дата генерации:** ${new Date().toLocaleString()}
**Обработано файлов:** ${totalFiles}
**Всего элементов (с учётом папок):** ${entries.length}

## Структура проекта

\`\`\`
${tree}
\`\`\`

## Оглавление

${tocItems.join("\n")}

## Содержимое файлов

`;

  const outputContent = header + fileBlocks.join("");
  const outputFilePath = path.join(startDir, "result.md");
  await fs.writeFile(outputFilePath, outputContent, "utf8");

  console.log(`\nСтатистика:
  Всего файлов: ${totalFiles}
  Успешно распарсено (текст + base64): ${parsedFiles}
  Бинарных >1 МБ (только метаданные): ${skippedBinaryLarge}
  Ошибок чтения: ${failedFiles}
Готово! Результат сохранён в ${outputFilePath}`);
}

main().catch((err) => {
  console.error("Ошибка:", err);
  process.exit(1);
});

```

// patches/development.diff (base64, size: 181, mtime: 2025-04-01T11:00:32.000Z, md5: cf6d24ef046ea404b2cb1debe3be97ef)

```
LS0tIHN0YXRpYy9tb2RlLmpzCisrKyBzdGF0aWMvbW9kZS5qcwpAQCAtMSwyICsxLDIgQEAKIC8vIFRoaXMgZmlsZSBpcyBhdXRvLWdlbmVyYXRlZCBieSB0aGUgc2V0LXJ1bi1tb2RlLmpzIHNjcmlwdAotbW9kdWxlLmV4cG9ydHMgPSAncHJvZHVjdGlvbic7Cittb2R1bGUuZXhwb3J0cyA9ICdkZXZlbG9wbWVudCc7Cg==
```

// patches/individual.diff (base64, size: 1018, mtime: 2025-04-01T11:00:32.000Z, md5: 0e4d3cb2ba27865d7d2c296628dc2d78)

```
LS0tIHN0YXRpYy9pbmRleC5qcworKysgc3RhdGljL2luZGV4LmpzCkBAIC0xLDcgKzEsMjcgQEAKIC8vIFdhcm5pbmc6IFlvdSBhbG1vc3QgY2VydGFpbmx5IGRvICpub3QqIHdhbnQgdG8gZWRpdCB0aGlzIGNvZGUgLQogLy8gaW5zdGVhZCwgeW91IHdhbnQgdG8gZWRpdCBzcmMvanMvbWFpbi5qc3ggaW5zdGVhZAoKK2Z1bmN0aW9uIFBhdGNoU25hcHNob3QoKSB7CisgIGNvbnN0IGVkbUxpdGVEID0gc25hcHNob3RSZXN1bHQuY3VzdG9tUmVxdWlyZSgnQGF4b3NvZnQvZWRtLWxpdGUtZC9zcmMvZC5qcycpOworICBzbmFwc2hvdFJlc3VsdC5jdXN0b21SZXF1aXJlLmNhY2hlWydAYXhvc29mdC9lZG0tbGl0ZS1kL3NyYy9kLmpzJ10gPSB7CisgICAgZXhwb3J0czogZnVuY3Rpb24oKSB7CisgICAgICBsZXQgcmVzcG9uc2UgPSBKU09OLnBhcnNlKGVkbUxpdGVEKC4uLmFyZ3VtZW50cykudG9TdHJpbmcoJ3V0ZjgnKSk7CisgICAgICBpZiAoJ2xpY2Vuc2VFeHBpcmVzQXQnIGluIHJlc3BvbnNlIHx8ICdsaWNlbnNlZEZlYXR1cmVzJyBpbiByZXNwb25zZSkgeworICAgICAgICByZXNwb25zZSA9IHsKKyAgICAgICAgICAuLi5yZXNwb25zZSwKKyAgICAgICAgICBhdmFpbGFibGVUcmlhbERheXM6IG51bGwsCisgICAgICAgICAgbGljZW5zZUV4cGlyZXNBdDogODY0MDAwMDAwMDAwMDAwMCwKKyAgICAgICAgICBsaWNlbnNlZEZlYXR1cmVzOiBbJ2luZGl2aWR1YWwnXQorICAgICAgICB9OworICAgICAgfQorICAgICAgcmV0dXJuIEJ1ZmZlci5mcm9tKEpTT04uc3RyaW5naWZ5KHJlc3BvbnNlKSwgJ3V0ZjgnKTsKKyAgICB9CisgIH07Cit9CisKIChmdW5jdGlvbigpIHsKKyAgUGF0Y2hTbmFwc2hvdCgpOworCiAgIGNvbnN0IFBlcmYgPSBzbmFwc2hvdFJlc3VsdC5jdXN0b21SZXF1aXJlKCcuL3NyYy9qcy91dGlscy9QZXJmb3JtYW5jZS5qcycpOwogICBQZXJmLnRpbWVFbmQoJ2xvYWRpbmcgbW9uYWNvIHNjcmlwdHMnKTsKICAgUGVyZi50aW1lKCdpbmRleC5qcyBwcmUtYm9vdHN0cmFwJyk7Cg==
```

// patches/pro.diff (base64, size: 1105, mtime: 2025-04-01T11:00:32.000Z, md5: c4f1a66bd16dd22686e132daee8636b1)

```
VGhpcyBmaWxlIHdvdWxkIGJlIGlnbm9yZWQgc2luY2UgR2l0Q3JhY2tlbiB2OC4yLjAuClRoZXJlIGlzIGEgbmV3IHBhdGNoZXIgd2l0aCBwYXR0ZXJuIG1hdGNoIG5vdy4KU2VlIHBhdGNoRGlyV2l0aFBybyBpbiBzcmMvcGF0Y2hlci50cyBmb3IgbW9yZSBkZXRhaWwuCi0tLSBzcmMvbWFpbi9zdGF0aWMvc3RhcnRNYWluUHJvY2Vzcy5qcworKysgc3JjL21haW4vc3RhdGljL3N0YXJ0TWFpblByb2Nlc3MuanMKQEAgLTEsNCArMSwxOSBAQAogLyogZXNsaW50LWRpc2FibGUgbm8tY29uc29sZSAqLworCitmdW5jdGlvbiBwYXRjaFNuYXBzaG90KCkgeworICB2YXIgUmVnaXN0cmF0aW9uSGVscGVycyA9IHNuYXBzaG90UmVzdWx0LmN1c3RvbVJlcXVpcmUoJy4vc3JjL3NoYXJlZE1vZHVsZXMvcmVnaXN0cmF0aW9uL3NoYXJlZC9SZWdpc3RyYXRpb25IZWxwZXJzLmpzJyk7CisgIHZhciB7IGRlY29kZUJvZHkgfSA9IFJlZ2lzdHJhdGlvbkhlbHBlcnM7CisgIFJlZ2lzdHJhdGlvbkhlbHBlcnMuZGVjb2RlQm9keSA9IGZ1bmN0aW9uKCkgeworICAgIHZhciBib2R5ID0gZGVjb2RlQm9keSguLi5hcmd1bWVudHMpOworICAgIGJvZHkgPSB7CisgICAgICAuLi5ib2R5LAorICAgICAgbGljZW5zZWRGZWF0dXJlczogWydwcm8nXSwKKyAgICAgIHByb0FjY2Vzc1N0YXRlOiB7fQorICAgIH0KKyAgICByZXR1cm4gYm9keTsKKyAgfQorfQorCiAoZnVuY3Rpb24oKSB7CiAgIGlmIChwcm9jZXNzLmVudi5HSVRLUkFLRU5fT1BFTl9NQUlOX0RFVl9UT09MUykgewogICAgIHJlcXVpcmUoJy4vc3RhcnREZXZUb29scycpOwpAQCAtMzIsNiArNDcsNyBAQAogICBnbG9iYWwubW9kZSA9IGJ1aWxkTW9kZTsKIAogICBpZiAodHlwZW9mIHNuYXBzaG90UmVzdWx0ICE9PSAndW5kZWZpbmVkJykgeworICAgIHBhdGNoU25hcHNob3QoKTsKICAgICBjb25zdCBwYXRoID0gcmVxdWlyZSgncGF0aCcpOwogICAgIHNuYXBzaG90UmVzdWx0LnNldEdsb2JhbHMoZ2xvYmFsLCBwcm9jZXNzLCBnbG9iYWwsIHt9LCBjb25zb2xlLCByZXF1aXJlLCBwYXRoLnJlc29sdmUoKSk7CiAgICAgc25hcHNob3RSZXN1bHQuY3VzdG9tUmVxdWlyZSgnLi9zcmMvbWFpbi9tYWluLmpzJyk7Cg==
```

// patches/selfhosted.diff (base64, size: 191, mtime: 2025-04-01T11:00:32.000Z, md5: 093083421cac0bbd576097c728df62b0)

```
LS0tIHN0YXRpYy9jbGllbnRUeXBlLmpzCisrKyBzdGF0aWMvY2xpZW50VHlwZS5qcwpAQCAtMSwyICsxLDIgQEAKIC8vIFRoaXMgZmlsZSBpcyBhdXRvLWdlbmVyYXRlZCBieSB0aGUgc2V0LWNsaWVudC10eXBlLmpzIHNjcmlwdAotbW9kdWxlLmV4cG9ydHMgPSAnTk9STUFMJzsKK21vZHVsZS5leHBvcnRzID0gJ0VOVEVSUFJJU0UnOwo=
```

// patches/staging.diff (base64, size: 177, mtime: 2025-04-01T11:00:32.000Z, md5: ff7d46b96de3b951874e81b13b02ad56)

```
LS0tIHN0YXRpYy9tb2RlLmpzCisrKyBzdGF0aWMvbW9kZS5qcwpAQCAtMSwyICsxLDIgQEAKIC8vIFRoaXMgZmlsZSBpcyBhdXRvLWdlbmVyYXRlZCBieSB0aGUgc2V0LXJ1bi1tb2RlLmpzIHNjcmlwdAotbW9kdWxlLmV4cG9ydHMgPSAncHJvZHVjdGlvbic7Cittb2R1bGUuZXhwb3J0cyA9ICdzdGFnaW5nJzsK
```

// patches/standalone.diff (base64, size: 877, mtime: 2025-04-01T11:00:32.000Z, md5: 90499744e9b525dbfee9f9f5596fc75f)

```
LS0tIHN0YXRpYy9jbGllbnRUeXBlLmpzCisrKyBzdGF0aWMvY2xpZW50VHlwZS5qcwpAQCAtMSwyICsxLDIgQEAKIC8vIFRoaXMgZmlsZSBpcyBhdXRvLWdlbmVyYXRlZCBieSB0aGUgc2V0LWNsaWVudC10eXBlLmpzIHNjcmlwdAotbW9kdWxlLmV4cG9ydHMgPSAnTk9STUFMJzsKK21vZHVsZS5leHBvcnRzID0gJ1NUQU5EQUxPTkUnOwotLS0gc3RhdGljL3N0YXJ0TWFpblByb2Nlc3MuanMKKysrIHN0YXRpYy9zdGFydE1haW5Qcm9jZXNzLmpzCkBAIC0xLDMgKzEsMTQgQEAKK2Z1bmN0aW9uIFBhdGNoU25hcHNob3QoKSB7CisgIHNuYXBzaG90UmVzdWx0LmN1c3RvbVJlcXVpcmUuY2FjaGVbJ0BheG9zb2Z0L2VkbS1kL3NyYy9kLmpzJ10gPSB7CisgICAgZXhwb3J0czogKCkgPT4KKyAgICAgIFByb21pc2UucmVzb2x2ZSh7CisgICAgICAgIGV4cGlyZXNBdDogODY0MDAwMDAwMDAwMDAwMCwKKyAgICAgICAgcXVhbnRpdHk6IE51bWJlci5NQVhfU0FGRV9JTlRFR0VSLAorICAgICAgICBzdWJzY3JpYmVyOiB7IG5hbWU6ICdLaWxsV29sZlZsYWQnIH0KKyAgICAgIH0pCisgIH07Cit9CisKIChmdW5jdGlvbiAoKSB7CiAgIHJlcXVpcmUoJy4vbG9hZFNuYXBzaG90JykKCkBAIC0yMyw2ICszNCw3IEBACgogICBpZiAodHlwZW9mIHNuYXBzaG90UmVzdWx0ICE9PSAndW5kZWZpbmVkJykgewogICAgIHNuYXBzaG90UmVzdWx0LnNldEdsb2JhbHMoZ2xvYmFsLCBwcm9jZXNzLCBnbG9iYWwsIHt9LCBjb25zb2xlLCByZXF1aXJlLCBnbG9iYWwuX19kaXJuYW1lICsgJy8uLicpOworICAgIFBhdGNoU25hcHNob3QoKTsKICAgICBzbmFwc2hvdFJlc3VsdC5jdXN0b21SZXF1aXJlKCcuL3NyYy9hcHBCb290c3RyYXAvbWFpbi5qcycpOwogICB9CiB9KSgpCg==
```

// README.md

```markdown
# GitCracken

GitKraken utils for non-commercial use

Working on `GNU/Linux` (without `snap`), `Windows` and `macOS`!

> WARNING! On `macOS` you should patch `GitKraken` only after first launch and full program closing!

## Requirements

- `Node.js` v12 LTS or later
- `yarn`

## Usage

- `yarn install`
- `yarn build`
- `node dist/bin/gitcracken.js --help` for more usage information

### Patcher

```bash
$ gitcracken patcher [options] [actions...]
```

`actions` - array of values (any order, any count)

> If `actions` is empty, will be used `auto` mode (ideal for beginners)

| Action   | Description                                 |
|----------|---------------------------------------------|
| `backup` | Backup `app.asar` file                      |
| `unpack` | Unpack `app.asar` file into `app` directory |
| `patch`  | Patch `app` directory                       |
| `pack`   | Pack `app` directory to `app.asar` file     |
| `remove` | Remove `app` directory                      |

| Option            | Description (if not defined, will be used `auto` value)         |
|-------------------|-----------------------------------------------------------------|
| `-a`, `--asar`    | Path to `app.asar` file                                         |
| `-d`, `--dir`     | Path to `app` directory                                         |
| `-f`, `--feature` | Patcher feature (from [patches](patches) dir without extension) |

> You can invoke `-f`, `--feature` several times to apply all patches!

### Examples

`Auto` patch installed `GitKraken` (maybe require `sudo` privileges on `GNU/Linux`)

```bash
$ gitcracken patcher
```

Use custom path to `app.asar`

```bash
$ gitcracken patcher --asar ~/Downloads/gitkraken/resources/app.asar
```

Use custom `actions` (`backup`, `unpack` and `patch`)

```bash
$ gitcracken patcher backup unpack patch
```

## Disable Automatic Update

Add this content to your `hosts` file:

```text
0.0.0.0 release.gitkraken.com
```

```

// src/appId.ts

```typescript
import * as crypto from "crypto";
import * as os from "os";
import * as path from "path";
import * as util from "util";

import * as fs from "fs-extra";
import {getMac} from "getmac";
import * as uuid from "uuid";

import {CURRENT_PLATFORM, Platforms} from "./platform";

/**
 * AppId
 */
export class AppId {
  /**
   * Generate AppId as sha1sum of specific mac address (or other string e.g. uuid/v4)
   * @param mac MAC address
   * @throws Error
   */
  public static generate(mac: string): string {
    return crypto
      .createHash("sha1")
      .update(mac, "utf8")
      .digest("hex");
  }

  /**
   * Generate AppId as sha1sum of your mac address (or as uuid/v4 if you don't have mac)
   * @throws Error
   */
  public static async generateAsync(): Promise<string> {
    let mac: string;
    try {
      mac = await util.promisify(getMac)();
    } catch {
      mac = uuid.v4();
    }
    return AppId.generate(mac);
  }

  /**
   * Read AppId from GitKraken config
   * @param config Path to config
   * @throws Error
   */
  public static read(config: string = AppId.configPath()): string | undefined {
    if (!config || !fs.existsSync(config)) {
      return undefined;
    }
    return fs.readJSONSync(config).appId;
  }

  /**
   * Default config path
   */
  private static configPath(): string {
    switch (CURRENT_PLATFORM) {
      case Platforms.linux:
      case Platforms.macOS:
        return path.join(os.homedir(), ".gitkraken/config");
      case Platforms.windows:
        return path.join(os.homedir(), "AppData/Roaming/.gitkraken/config");
    }
  }
}

```

// src/index.ts

```typescript
export * from "./appId";
export * from "./logo";
export * from "./patcher";
export * from "./platform";
export * from "./secFile";

```

// src/logo.ts

```typescript
import chalk from "chalk";
import * as figlet from "figlet";

import {packageJson} from "../global";

/**
 * Logo
 */
export class Logo {
  /**
   * Print logo
   * @param text Main text
   */
  public static print(text = "GitCracken"): void {
    console.log();
    console.log(figlet.textSync(text, "ANSI Shadow"));
    console.log(`${chalk.bold("• Description")}: ${packageJson.description}`);
    console.log(`${chalk.bold("• Version")}: ${packageJson.version}`);
    console.log(`${chalk.bold("• Author")}: ${packageJson.author}`);
    console.log(`${chalk.bold("• License")}: ${packageJson.license}`);
    console.log(`${chalk.bold("• Home Page")}: ${packageJson.homepage}`);
    console.log();
  }
}

```

// src/patcher.ts

```typescript
import * as os from "os";
import * as path from "path";

import * as asar from "asar";
import * as diff from "diff";
import * as fs from "fs-extra";
import natsort from "natsort";

import {baseDir} from "../global";
import {CURRENT_PLATFORM, Platforms} from "./platform";

/**
 * Patcher options
 */
export interface IPatcherOptions {
  /**
   * app.asar file
   */
  readonly asar?: string;

  /**
   * app directory
   */
  readonly dir?: string;

  /**
   * Patcher features
   */
  readonly features: string[];
}

/**
 * Patcher
 */
export class Patcher {
  private static findAsarUnix(...files: string[]): string | undefined {
    return files.find((file) => fs.existsSync(file));
  }

  private static findAsarLinux(): string | undefined {
    return Patcher.findAsarUnix(
      "/opt/gitkraken/resources/app.asar", // Arch Linux
      "/usr/share/gitkraken/resources/app.asar", // deb & rpm
    );
  }

  private static findAsarWindows(): string | undefined {
    const gitkrakenLocal = path.join(os.homedir(), "AppData/Local/gitkraken");
    if (!fs.existsSync(gitkrakenLocal)) {
      return undefined;
    }
    const apps = fs
      .readdirSync(gitkrakenLocal)
      .filter((item) => item.match(/^app-\d+\.\d+\.\d+$/));
    let app = apps.sort(natsort({desc: true}))[0];
    if (!app) {
      return undefined;
    }
    app = path.join(gitkrakenLocal, app, "resources/app.asar");
    return fs.existsSync(app) ? app : undefined;
  }

  private static findAsarMacOS(): string | undefined {
    return Patcher.findAsarUnix(
      "/Applications/GitKraken.app/Contents/Resources/app.asar",
    );
  }

  private static findAsar(dir?: string): string | undefined {
    if (dir) {
      return path.normalize(dir) + ".asar";
    }
    switch (CURRENT_PLATFORM) {
      case Platforms.linux:
        return Patcher.findAsarLinux();
      case Platforms.windows:
        return Patcher.findAsarWindows();
      case Platforms.macOS:
        return Patcher.findAsarMacOS();
    }
  }

  private static findDir(asarFile: string): string {
    return path.join(
      path.dirname(asarFile),
      path.basename(asarFile, path.extname(asarFile)),
    );
  }

  private readonly _asar: string;
  private readonly _dir: string;
  private readonly _features: string[];

  /**
   * Patcher constructor
   * @param options Options
   */
  public constructor(options: IPatcherOptions) {
    const maybeAsar = options.asar || Patcher.findAsar(options.dir);
    if (!maybeAsar) {
      throw new Error("Can't find app.asar!");
    }
    this._asar = maybeAsar;
    this._dir = options.dir || Patcher.findDir(this.asar);
    this._features = options.features;
    if (!this.features.length) {
      throw new Error("Features is empty!");
    }
  }

  /**
   * app.asar file
   */
  public get asar(): string {
    return this._asar;
  }

  /**
   * app directory
   */
  public get dir(): string {
    return this._dir;
  }

  /**
   * Patcher features
   */
  public get features(): string[] {
    return this._features;
  }

  /**
   * Backup app.asar file
   * @throws Error
   */
  public backupAsar(): string {
    const backup = `${this.asar}.${new Date().getTime()}.backup`;
    fs.copySync(this.asar, backup);
    return backup;
  }

  /**
   * Unpack app.asar file into app directory
   * @throws Error
   */
  public unpackAsar(): void {
    asar.extractAll(this.asar, this.dir);
  }

  /**
   * Pack app directory to app.asar file
   * @throws Error
   */
  public packDirAsync(): Promise<void> {
    return asar.createPackage(this.dir, this.asar);
  }

  /**
   * Remove app directory
   * @throws Error
   */
  public removeDir(): void {
    fs.removeSync(this.dir);
  }

  /**
   * Patch app directory
   * @throws Error
   */
  public patchDir(): void {
    for (const feature of this.features) {
      switch (feature) {
        case "pro":
          this.patchDirWithPro();
          break;

        default:
          this.patchDirWithFeature(feature);
          break;
      }
    }
  }

  private patchDirWithPro(): void {
    const bundlePath = path.join(this.dir, "src/main/static/main.bundle.js");

    const patchedPattern = '(delete json.proAccessState,delete json.licenseExpiresAt,json={...json,licensedFeatures:["pro"]});';

    const pattern1 = /const [^=]*="dev"===[^?]*\?"[\w+/=]+":"[\w+/=]+";/;
    const pattern2 = /return (JSON\.parse\(\([^;]*?\)\(Buffer\.from\([^;]*?,"base64"\)\.toString\("utf8"\),Buffer\.from\([^;]*?\.secure,"base64"\)\)\.toString\("utf8"\)\))\};/;
    const searchValue = new RegExp(`(?<=${pattern1.source})${pattern2.source}`)
    const replaceValue =
      "var json=$1;" +
      '("licenseExpiresAt"in json||"licensedFeatures"in json)&&' +
      '(delete json.proAccessState,delete json.licenseExpiresAt,json={...json,licensedFeatures:["pro"]});' +
      "return json};";

    const sourceData = fs.readFileSync(bundlePath, "utf-8");
    const sourcePatchedData = sourceData.replace(searchValue, replaceValue);
    if (sourceData === sourcePatchedData) {
      if (sourceData.indexOf(patchedPattern) < 0) throw new Error("Can't patch pro features, pattern match failed. Get support from https://t.me/gitkrakencrackchat");
      throw new Error("It's already patched.");
    }
    fs.writeFileSync(bundlePath, sourcePatchedData, "utf-8");
  }

  private patchDirWithFeature(feature: string): void {
    const patches = diff.parsePatch(
      fs.readFileSync(path.join(baseDir, "patches", `${feature}.diff`), "utf8"),
    );
    for (const patch of patches) {
      this.patchDirWithPatch(patch);
    }
  }

  private patchDirWithPatch(patch: diff.ParsedDiff): void {
    const sourceData = fs.readFileSync(
      path.join(this.dir, patch.oldFileName!),
      "utf8",
    );
    const sourcePatchedData = diff.applyPatch(sourceData, patch);
    if ((sourcePatchedData as any) === false) {
      throw new Error(`Can't patch ${patch.oldFileName}`);
    }
    if (patch.oldFileName !== patch.newFileName) {
      fs.unlinkSync(path.join(this.dir, patch.oldFileName!));
    }
    fs.writeFileSync(
      path.join(this.dir, patch.newFileName!),
      sourcePatchedData,
      "utf8",
    );
  }
}

```

// src/platform.ts

```typescript
import chalk from "chalk";

/**
 * Platforms
 */
export enum Platforms {
  linux = 1,
  windows = 2,
  macOS = 3,
}

/**
 * Current platform
 */
export const CURRENT_PLATFORM: Platforms = ((): Platforms => {
  if (process.platform === "linux") {
    return Platforms.linux;
  }
  if (process.platform === "win32") {
    return Platforms.windows;
  }
  if (process.platform === "darwin") {
    return Platforms.macOS;
  }
  console.error(
    `We Are Deeply Sorry! Your OS ${chalk.red.bold(
      process.platform,
    )} is not supported!`,
  );
  return process.exit(1);
})();

```

// src/secFile.ts

```typescript
import * as crypto from "crypto";

import * as fs from "fs-extra";

/**
 * Password protected file with JSON content.
 * This class is fork of <https://gitlab.com/maxkorp/secure-storage> library
 */
export class SecFile<T = object> {
  private readonly _fileName: string;
  private readonly _password: string;
  private readonly _algorithm: string;
  private _data?: T;

  /**
   * Password protected file with JSON content constructor
   * @param fileName File name
   * @param password Password
   * @param algorithm Algorithm
   */
  public constructor(
    fileName: string,
    password: string,
    algorithm: string = "aes-256-cbc",
  ) {
    this._fileName = fileName;
    this._password = password;
    this._algorithm = algorithm;
  }

  /**
   * File name
   */
  public get fileName(): string {
    return this._fileName;
  }

  /**
   * Password
   */
  public get password(): string {
    return this._password;
  }

  /**
   * Algorithm
   */
  public get algorithm(): string {
    return this._algorithm;
  }

  /**
   * Data
   */
  public get data(): T | undefined {
    return this._data;
  }

  /**
   * Read data from disk
   * @throws Error
   */
  public read(): void {
    const decipher = crypto.createDecipher(this.algorithm, this.password); // TODO: deprecated!
    this._data = JSON.parse(
      Buffer.concat([
        decipher.update(fs.readFileSync(this.fileName)),
        decipher.final(),
      ]).toString("utf8"),
    );
  }
}

```

// tsconfig.json

```json
{
  "compilerOptions": {
    "lib": [
      "esnext"
    ],
    "module": "commonjs",
    "moduleResolution": "node",
    "newLine": "lf",
    "outDir": "./dist",
    "removeComments": true,
    "sourceMap": false,
    "strict": true,
    "target": "esnext"
  },
  "exclude": [
    "./node_modules"
  ],
  "include": [
    "./**/*.ts"
  ]
}

```

// tslint.json

```json
{
  "extends": [
    "tslint:recommended",
    "tslint-config-prettier"
  ],
  "rules": {
    "no-console": false,
    "no-reference": false,
    "prettier": true,
    "variable-name": [
      true,
      "allow-leading-underscore"
    ]
  },
  "rulesDirectory": [
    "tslint-plugin-prettier"
  ]
}

```

// types/asar.d.ts

```typescript
declare module "asar" {
  export function extractAll(archive: string, dest: string): void;

  export function createPackage(src: string, dest: string): Promise<void>;
}

```

// types/getmac.d.ts

```typescript
declare module "getmac" {
  export function getMac(callback: (error: Error | null, mac: string) => void): void;
}

```

// types/index.d.ts

```typescript
/// <reference path="./asar.d.ts" />
/// <reference path="./getmac.d.ts" />

```

