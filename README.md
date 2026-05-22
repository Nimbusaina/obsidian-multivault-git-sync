# obsidian-multivault-git-sync
produced by ChatGPT

# MultiVault Git Sync

A conservative Git synchronization plugin for Obsidian.

Designed for:

```text
One Git repository
↳ Multiple independent Obsidian vaults
↳ Multiple devices
↳ Manual / semi-automatic synchronization
```

---

# 1. Plugin Purpose

This plugin was created to solve several practical problems encountered when synchronizing multiple Obsidian vaults across multiple devices through GitHub.

Typical structure:

```text
Obsidian library/
├── My notebook/
├── My blog/
├── File vault/
├── HWK-Obsidian/
└── .git/
```

Each subdirectory is an independent Obsidian vault.

The entire directory is managed by a single Git repository.

The plugin provides:

* Cross-vault Git synchronization
* Repository-level locking
* Conservative conflict handling
* Large-file protection
* Multi-device synchronization support

---

# 2. Applicable Environment

Recommended environment:

| Component       | Recommendation                            |
| --------------- | ----------------------------------------- |
| OS              | Windows 10 / 11                           |
| Obsidian        | Desktop version                           |
| Git             | Installed and available in PATH           |
| GitHub          | HTTPS repository                          |
| Repository type | One repository containing multiple vaults |
| Sync style      | Manual sync preferred                     |

Recommended structure:

```text
F:\Obsidian library\
├── VaultA\
├── VaultB\
├── VaultC\
└── .git\
```

Different devices do NOT need identical paths.

Example:

```text
PC A:
F:\Obsidian library\

PC B:
E:\Obsidian library\
```

is fully supported.

---

# 3. Difference from Obsidian Git Plugin

## Obsidian Git Plugin

Typical assumptions:

```text
One vault
↳ One Git repository
```

Characteristics:

* Strong automation
* Aggressive auto-pull / auto-push
* Optimized for single-vault usage
* Plugin configuration synchronized through vault
* Limited multi-vault repository support

Potential problems in multi-vault repository usage:

* Multiple vaults simultaneously running Git operations
* index.lock conflicts
* Plugin configuration synchronization conflicts
* workspace.json conflicts
* Rebase interruptions
* Plugin self-update conflicts

---

## MultiVault Git Sync

Design assumptions:

```text
One repository
↳ Multiple vaults
↳ Multiple devices
```

Characteristics:

* Conservative synchronization strategy
* Repository-level lock
* Manual-first workflow
* Rebase-state detection
* Large-file detection
* Local-change backup before pull
* Conflict-stop strategy

The plugin intentionally avoids:

* Automatic Markdown merge
* Automatic conflict overwrite
* Fully autonomous background synchronization

---

# 4. Version History

## v0.1.x

Initial implementation.

Functions:

* Multi-vault repository synchronization
* Repository lock mechanism
* Basic pull / commit / push flow
* Startup sync
* Interval sync

Problems discovered during practical usage:

* Plugin self-conflicts
* workspace.json conflicts
* Interrupted rebases
* Conflict backup directories entering Git history
* Large PDF upload failures
* Simultaneous multi-device plugin-setting conflicts

---

## v0.2.0

Stabilization version.

New features:

### 1. Conflict Backup

Before pull:

```text
Modified local files
→ copied into:
.obsidian-git-conflicts/
```

Used to prevent accidental local-content loss.

---

### 2. Rebase / Merge Detection

The plugin detects:

```text
rebase-merge
MERGE_HEAD
CHERRY_PICK_HEAD
```

Synchronization stops immediately if repository state is unsafe.

---

### 3. Large File Protection

Default thresholds:

```text
>50 MB  → warning
>100 MB → synchronization blocked
```

Designed for GitHub limitations.

---

### 4. Conflict Commands

Added commands:

```text
Show Git conflict status
Abort current Git rebase or merge
Scan large files before Git commit
```

---

### 5. Repository Lock

Global lock file:

```text
.git/obsidian-multivault-sync.lock
```

Prevents simultaneous synchronization from multiple vaults.

---

# 5. Installation

## 5.1 Install Git

Download:

[Git for Windows](https://git-scm.com/download/win?utm_source=chatgpt.com)

Confirm:

```powershell
git --version
```

---

## 5.2 Install GitHub CLI

Download:

[GitHub CLI](https://cli.github.com?utm_source=chatgpt.com)

Login:

```powershell
gh auth login
```

---

## 5.3 Clone Repository

Example:

```powershell
cd F:\
git clone https://github.com/USERNAME/REPOSITORY.git "Obsidian library"
```

---

## 5.4 Install Plugin

Place:

```text
main.js
manifest.json
```

into:

```text
Vault/.obsidian/plugins/multivault-git-sync/
```

Enable plugin in Obsidian.

---

# 6. Repository Configuration

Recommended GitHub repository settings:

| Setting    | Recommendation |
| ---------- | -------------- |
| Visibility | Private        |
| License    | None initially |
| README     | Optional       |
| .gitignore | Required       |

Recommended `.gitignore`:

```gitignore
*.pdf

.obsidian-git-conflicts/

**/.obsidian/workspace.json
**/.obsidian/workspaces.json

**/.obsidian/plugins/multivault-git-sync/
```

---

# 7. Plugin Configuration

## Repository Root

Must point to:

```text
Directory containing .git
```

Example:

```text
F:\Obsidian library
```

NOT:

```text
F:\Obsidian library\My notebook
```

---

## Recommended Sync Settings

Only ONE vault should enable automatic synchronization.

Recommended:

| Setting            | Main Vault | Other Vaults |
| ------------------ | ---------- | ------------ |
| Sync on startup    | ON         | OFF          |
| Auto sync interval | 5~10 min   | 0            |

This prevents repository-lock contention.

---

# 8. Synchronization Workflow

Recommended workflow:

## Device A

```text
Open vault
→ Sync repository now
→ Edit
→ Sync repository now
```

---

## Device B

```text
Open vault
→ Sync repository now
→ Edit
→ Sync repository now
```

Avoid:

```text
Two devices editing same file simultaneously
```

---

# 9. Conflict Handling

The plugin intentionally uses:

```text
Stop-on-conflict
```

instead of automatic merge.

If conflicts occur:

```text
Synchronization stops immediately
```

Local changes remain recoverable through:

```text
.obsidian-git-conflicts/
```

---

## Common Conflict Types

### 1. Same file modified on two devices

Example:

```text
a.md
```

modified simultaneously.

Git conflict occurs.

Manual resolution required.

---

### 2. modify/delete conflict

Example:

```text
Device A:
delete a.md

Device B:
modify a.md
```

Git requires manual decision.

---

### 3. Plugin configuration conflicts

Solved by excluding:

```text
multivault-git-sync/data.json
```

from synchronization.

---

# 10. Large File Recommendations

GitHub is not suitable for large textbook / dataset repositories.

Recommended:

| Content        | Recommendation          |
| -------------- | ----------------------- |
| Markdown       | GitHub                  |
| Small images   | GitHub                  |
| PDFs           | OneDrive / NAS / Zotero |
| Large datasets | External storage        |

Avoid synchronizing:

```text
100MB+
```

files through GitHub.

---

# 11. Current Limitations

Not yet implemented:

* Automatic Markdown merge
* Visual diff UI
* Automatic conflict-copy generation
* Vault-level selective synchronization
* Git LFS integration
* Mobile support

---

# 12. Recommended Usage Philosophy

This plugin prioritizes:

```text
Repository safety
over
Maximum automation
```

Synchronization should remain:

* Explicit
* Observable
* Interruptible
* Recoverable

rather than fully autonomous.

