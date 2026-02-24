---
id: r-cdaa
status: closed
deps: [r-9659]
created: 2026-02-22T00:31:52Z
type: feature
priority: 0
assignee: Adam
updated: 2026-02-22T01:31:08Z
tags: [marketplace, registry, skill-install]
---
# Cross-harness skill install from registry

Extend `mlld skill install` to resolve and install skills from the mlld registry, not just the built-in mlld plugin. Users should be able to run `mlld skill install @author/skill-name` to download a skill from the registry and install it to all detected harnesses.

## Current Architecture

`cli/commands/skill.ts` has a single flow:

- `skillInstall(target, scope, verbose, local)` — always installs the built-in mlld plugin from `plugins/mlld/`
- `copySkills(sourceDir, targetDir)` — copies `skills/` and `examples/` from plugin source to `{targetDir}/skills/mlld/`
- `installHarness(harness, sourceDir, scope, verbose, local)` — per-harness install logic
  - Claude Code: delegates to `pluginInstall()` which runs `claude plugin marketplace add` + `claude plugin install`
  - Codex/OpenCode: `copySkills()` to `~/.codex/skills/mlld/` or `~/.config/opencode/skills/mlld/`
  - Pi: copies to `~/.pi/agent/skills/mlld/`
- Version tracking: single `.version` file at `{harnessDir}/skills/mlld/.version`
- `skillUninstall()` — removes `{harnessDir}/skills/mlld/` directory
- `skillStatus()` — reads `.version` file, delegates to `pluginStatus()` for Claude Code

The command parser in `createSkillCommand()` takes `args[0]` as subcommand (`install`/`uninstall`/`status`) and reads `flags.target`, `flags.scope`, `flags.local`, `flags.verbose`.

## Implementation Plan

### 1. Argument parsing — accept `@author/name` in install/uninstall

In `createSkillCommand().execute()`, after extracting the subcommand from `args[0]`, check if `args[1]` matches `@author/name` pattern:

```typescript
const subcommand = args[0];
const moduleRef = args[1]; // e.g., "@alice/my-helper"
const isRegistryRef = moduleRef && /^@[a-z0-9-]+\/[a-z0-9-]+$/.test(moduleRef);

switch (subcommand) {
  case 'install':
    if (isRegistryRef) {
      await skillInstallFromRegistry(moduleRef, target, scope, verbose);
    } else {
      await skillInstall(target, scope, verbose, local);
    }
    break;
  case 'uninstall':
    if (isRegistryRef) {
      await skillUninstallRegistry(moduleRef, target, verbose);
    } else {
      await skillUninstall(target, verbose);
    }
    break;
```

### 2. Registry resolution — `skillInstallFromRegistry()`

New function that:

1. Creates a `RegistryResolver` and resolves the module reference
2. The resolver already handles directory modules — `resolveDirectoryModule()` fetches all files and returns them in `metadata.directoryFiles`
3. Validates `metadata.moduleType === 'skill'`, rejects with clear error otherwise
4. Returns the resolved files to the per-harness install logic

```typescript
import { RegistryResolver } from '@core/resolvers';

async function skillInstallFromRegistry(
  moduleRef: string, target: string | undefined, scope: string, verbose?: boolean
): Promise<void> {
  const resolver = new RegistryResolver();

  // Resolve from registry — fetches all directory files
  const resolution = await resolver.resolve(moduleRef);
  const metadata = resolution.metadata ?? {};

  if (!metadata.isDirectory || metadata.moduleType !== 'skill') {
    console.error(chalk.red(`${moduleRef} is not a skill module (type: ${metadata.moduleType || 'library'})`));
    process.exit(1);
  }

  const files = metadata.directoryFiles as Record<string, string>;
  const version = metadata.version || 'unknown';
  const simpleName = moduleRef.replace(/^@[^/]+\//, ''); // "my-helper"

  // Install to each harness
  const harnesses = target
    ? [detectHarness(target as HarnessName)].filter(h => h)
    : detectAll();
  const detected = harnesses.filter(h => h.detected);

  console.log(chalk.blue(`Installing ${moduleRef} (v${version})...\n`));

  for (const harness of detected) {
    console.log(chalk.bold(harness.name));
    await installRegistrySkill(harness, simpleName, files, version, scope);
    console.log();
  }
}
```

### 3. Per-harness registry skill install — `installRegistrySkill()`

New function. Key difference from the built-in flow: writes individual files from the resolved directory data rather than copying from a local directory.

```typescript
async function installRegistrySkill(
  harness: HarnessInfo, skillName: string,
  files: Record<string, string>, version: string, scope: string
): Promise<void> {
  switch (harness.name) {
    case 'Claude Code': {
      // Option A: use the marketplace (if skill is at repo root)
      //   claude plugin marketplace add mlld-lang/registry
      //   claude plugin install author--skillName@mlld-registry --scope user
      // Option B: write files to a temp dir and install as local plugin
      //   This is more reliable since it doesn't depend on marketplace
      const tmpDir = join(os.tmpdir(), `mlld-skill-${skillName}`);
      writeFilesTo(tmpDir, files);
      // Use claude plugin with local source
      // ... or just write SKILL.md to ~/.claude/skills/skillName/

      // Simplest approach: write the skill directory to project .claude/skills/
      const skillDir = join(process.cwd(), '.claude', 'skills', skillName);
      writeFilesTo(skillDir, filterSkillFiles(files));
      writeVersionMarker(skillDir, version);
      console.log(chalk.green(`  Installed to .claude/skills/${skillName}/`));
      break;
    }
    case 'Codex': {
      if (!harness.path) return;
      const target = join(harness.path, 'skills', skillName);
      writeFilesTo(target, filterSkillFiles(files));
      writeVersionMarker(target, version);
      console.log(chalk.green(`  Installed to ${target}/`));
      break;
    }
    // Pi, OpenCode: similar pattern with their specific paths
  }
}
```

**`filterSkillFiles(files)`**: From the full directory module files, extract only the skill-relevant files. A skill module has:
- `skills/{name}/SKILL.md` — the actual skill content
- Possibly `skills/{name}/*.md` — supporting docs

For non-Claude-Code harnesses, only the skill markdown files matter. For Claude Code, the full plugin structure (including `.claude-plugin/plugin.json`) is needed.

**`writeFilesTo(dir, files)`**: Helper that writes `Record<string, string>` to disk:
```typescript
function writeFilesTo(dir: string, files: Record<string, string>): void {
  mkdirSync(dir, { recursive: true });
  for (const [relativePath, content] of Object.entries(files)) {
    const fullPath = join(dir, relativePath);
    mkdirSync(dirname(fullPath), { recursive: true });
    writeFileSync(fullPath, content, 'utf8');
  }
}
```

### 4. Version tracking — per-skill `.version` files

Current: single `{harnessDir}/skills/mlld/.version` tracks the built-in plugin version.

New: each registry skill gets its own version marker at `{harnessDir}/skills/{skillName}/.version`.

```typescript
function writeVersionMarker(skillDir: string, version: string): void {
  writeFileSync(join(skillDir, '.version'), version, 'utf8');
}

function readSkillVersion(skillDir: string): string | null {
  try {
    return readFileSync(join(skillDir, '.version'), 'utf8').trim();
  } catch { return null; }
}
```

### 5. Status — enumerate registry-installed skills

`skillStatus()` currently only shows the built-in mlld plugin. Extend to scan for other skill directories:

```typescript
async function skillStatus(verbose?: boolean): Promise<void> {
  // ... existing harness detection ...

  for (const harness of detected) {
    // Built-in plugin status (existing logic)
    // ...

    // Registry-installed skills
    const skillsDir = getSkillsBaseDir(harness); // e.g., ~/.codex/skills/
    if (skillsDir && existsSync(skillsDir)) {
      const entries = readdirSync(skillsDir, { withFileTypes: true });
      for (const entry of entries) {
        if (!entry.isDirectory() || entry.name === 'mlld') continue; // skip built-in
        const ver = readSkillVersion(join(skillsDir, entry.name));
        if (ver) {
          console.log(`  ${entry.name}: ${chalk.green(ver)}`);
        }
      }
    }
  }
}
```

### 6. Uninstall — `skillUninstallRegistry()`

```typescript
async function skillUninstallRegistry(
  moduleRef: string, target: string | undefined, verbose?: boolean
): Promise<void> {
  const skillName = moduleRef.replace(/^@[^/]+\//, '');
  const harnesses = target
    ? [detectHarness(target as HarnessName)].filter(h => h)
    : detectAll();

  for (const harness of harnesses.filter(h => h.detected)) {
    const skillDir = getSkillDir(harness, skillName);
    if (skillDir && existsSync(skillDir)) {
      rmSync(skillDir, { recursive: true, force: true });
      console.log(chalk.green(`  Removed ${skillName} from ${harness.name}`));
    }
  }
}
```

### 7. Help text update

Update `showUsage()` to document the new syntax:

```
Usage:
  mlld skill install                       Install built-in mlld skills
  mlld skill install @author/skill-name    Install a skill from the registry
  mlld skill uninstall                     Remove built-in mlld skills
  mlld skill uninstall @author/skill-name  Remove a registry skill
  mlld skill status                        Check installation state
```

## Key Design Decisions

**Claude Code install strategy**: For Claude Code, there are two options:
- **A. Plugin marketplace**: `claude plugin marketplace add mlld-lang/registry` + `claude plugin install`. This makes the skill a proper Claude Code plugin with lifecycle management, but requires the marketplace to be set up and the skill at repo root.
- **B. Direct file copy**: Write skill files to `.claude/skills/{name}/` or `~/.claude/skills/{name}/`. Simpler, always works, but bypasses plugin lifecycle.

Recommend **Option B** for initial implementation — it's consistent with how other harnesses work, doesn't require marketplace setup, and always works regardless of where the skill was published from. Option A can be added later as an enhancement.

**Scope handling**: For the initial implementation, registry skills install to the project (`.claude/skills/`) rather than user scope. The `--scope` flag can optionally switch to `~/.claude/skills/`.

## Files to Modify

| File | Change |
|------|--------|
| `cli/commands/skill.ts` | Add `@author/name` argument parsing, `skillInstallFromRegistry()`, `installRegistrySkill()`, `skillUninstallRegistry()`, version tracking helpers, status scanning, help text |

## Dependencies

- `RegistryResolver` already handles directory modules (fetches all files, returns in `metadata.directoryFiles`)
- No changes needed to resolver or installer — we use `RegistryResolver` directly

## Acceptance Criteria

- [ ] `mlld skill install @author/skill-name` resolves from registry
- [ ] Validates module type is 'skill', rejects other types with clear error
- [ ] Downloads skill files from source repo
- [ ] Installs to all detected harnesses (Claude Code, Codex, Pi, OpenCode)
- [ ] Version tracking works per-skill (not just the built-in plugin)
- [ ] `mlld skill status` shows registry-installed skills alongside built-in ones
- [ ] `mlld skill uninstall @author/skill-name` removes a registry-installed skill
- [ ] Built-in mlld plugin install flow (`mlld skill install` with no args) still works
