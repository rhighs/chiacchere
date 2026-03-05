# chiacchiere

> *"chiacchiere"* — Italian for gossip, chatter. Also: the thing that spreads what your AI learned around the team.

---

Your agent figures something out. Normally that knowledge dies when the session ends.
**chiacchiere** syncs any skill file to a shared team repo so everyone's agent benefits.

```
skill file → chiacchiere opens PR → team reviews → everyone pulls
```

---

## How It Works

1. You have a skill file — from [amarcord](https://github.com/rhighs/amarcord), written by hand, or generated any other way
2. **chiacchiere** copies it to the shared team repo and opens a PR
3. Team reviews and merges
4. Everyone pulls — both opencode and Claude Code versions are ready

Skills are namespaced by project (`my-project:some-fix.md`, `general:some-pattern.md`),
auto-detected from the current git remote.

Before creating a new skill, chiacchiere checks for duplicates — updates existing
skills instead of creating noise.

---

## Setup

### 1. Create a shared team repo

Use the template in `team-repo-template/` or create a fresh repo:

```bash
gh repo create your-org/team-skills --private
```

### 2. Install on each machine

```bash
curl -fsSL https://raw.githubusercontent.com/rhighs/chiacchiere/main/install.sh | bash -s -- https://github.com/YOUR-ORG/team-skills
```

This:
- Clones the team repo
- Symlinks all skills into place via `stow` (opencode + Claude Code)
- Writes config to `~/.config/chiacchiere/team.conf`

### 3. Sync a skill

```bash
chiacchiere sync path/to/my-skill.md
```

That's it. Works with any `.md` skill file — no specific tool required.

---

## Trigger via AI

You can also let your agent sync for you. Just say:

> "sync this skill" / "share this to the team" / `/chiacchiere sync`

The AI will call `chiacchiere sync <file>` directly.

If you use [amarcord](https://github.com/rhighs/amarcord) for extraction, it can call
chiacchiere automatically after every session — but this is optional.

---

## Pull latest from the team

```bash
chiacchiere pull
```

---

## Namespace convention

`{project}:{description}.md` — auto-detected from `git remote get-url origin`.

```
my-project:mcp-proxy-namespaced-params.md
general:git-rebase-conflict-resolution.md
pleasetriage:vercel-dns-config.md
```

---

## Team repo structure

```
team-skills/
  opencode/              → stowed to ~/.config/opencode/commands/
    my-project:fix.md
  claude/                → stowed to ~/.claude/skills/
    my-project:fix/
      SKILL.md
  README.md
```

---

## Requirements

- `git`, `gh` (GitHub CLI)
- `stow` (`brew install stow` / `apt install stow`)

---

## Using with amarcord

[amarcord](https://github.com/rhighs/amarcord) is a session knowledge extraction tool — it pulls reusable skills out of AI coding sessions and saves them locally.

chiacchiere pairs naturally with it:

```
amarcord extracts skill → chiacchiere syncs to team → everyone pulls
```

**Setup:**

```bash
# install amarcord
curl -fsSL https://raw.githubusercontent.com/rhighs/amarcord/main/install.sh | bash

# install chiacchiere
curl -fsSL https://raw.githubusercontent.com/rhighs/chiacchiere/main/install.sh | bash -s -- https://github.com/YOUR-ORG/team-skills
```

**Workflow:**

1. Run `/amarcord` at the end of a coding session — it extracts a skill and saves it to `~/.config/opencode/commands/`
2. Run `chiacchiere sync <that-file>` to open a PR to the team repo
3. Teammates run `chiacchiere pull` to get it

Or just tell the AI: *"sync this skill to the team"* — it'll handle both steps.

amarcord and chiacchiere are independent tools. You don't need one to use the other.
