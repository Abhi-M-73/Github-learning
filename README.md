# Git & GitHub Practical Documentation

## 1. Git aur GitHub kya hai?

### Git

Git ek **version control system** hai. Ye tumhare project ke code ki history maintain karta hai.

Example:

```text
Version 1 → Version 2 → Version 3 → Version 4
```

Agar Version 4 me problem aa gayi, to Git ki help se purani state par ja sakte ho.

### GitHub

GitHub ek online platform hai jahan Git repositories ko remotely store kiya jata hai.

```text
Local Computer
     ↓
    Git
     ↓
  GitHub
```

---

# 2. Basic Git Workflow

Normally daily development me flow ye hota hai:

```bash
git status
```

Changes check karo.

```bash
git add .
```

Changes staging area me add karo.

```bash
git commit -m "Add login functionality"
```

Commit banao.

```bash
git push origin main
```

GitHub par push karo.

Complete flow:

```text
Code Change
    ↓
git status
    ↓
git add .
    ↓
git commit
    ↓
git push
    ↓
GitHub
```

---

# 3. Repository ki current state check karna

## Current changes

```bash
git status
```

Ye batayega:

* Kaunsi files modified hain
* Kaunsi files staged hain
* Kaunsi files untracked hain
* Current branch kya hai

---

# 4. Commit History Dekhna

Simple:

```bash
git log --oneline
```

Example:

```text
98215a7 Implement feature X
d41c989 .
1ad3f41 fix: Rename startData to startDate
41cde3c fix: Correct typo
95d0cfb feat: Add remaining balance
```

Detailed history:

```bash
git log
```

Graph ke saath:

```bash
git log --oneline --graph --decorate --all
```

---

# 5. Commit ka Hash kya hota hai?

Example:

```text
98215a7 Implement feature X
```

Yahan:

```text
98215a7
```

commit ka short hash hai.

Har commit ka unique identifier hota hai.

Is hash ki help se tum kisi specific commit par:

* ja sakte ho
* restore kar sakte ho
* compare kar sakte ho
* revert kar sakte ho

---

# 6. GitHub se latest commit remove karna

## Case 1 — Latest commit galat hai aur uske changes bhi remove karne hain

Example:

```text
A → B → C
```

`C` galat hai.

Use:

```bash
git reset --hard HEAD~1
```

Phir GitHub update:

```bash
git push origin main --force
```

### Important

`--hard` commit ke saath uske code changes bhi remove karega.

---

# 7. Latest 2 commits remove karna

Example:

```text
C = latest commit
B = second latest commit
A = correct commit
```

Agar B aur C dono remove karne hain:

```bash
git reset --hard HEAD~2
```

Phir:

```bash
git push origin main --force
```

Result:

```text
Before:

A → B → C

After:

A
```

---

# 8. Mere Case jaisa Example

Agar history hai:

```text
98215a7 Implement feature X
d41c989 .
1ad3f41 fix: Rename startData to startDate
41cde3c fix: Correct typo
95d0cfb feat: Add remaining balance
```

Aur tumhe:

```text
98215a7
d41c989
```

remove karna hai.

To:

```bash
git reset --hard HEAD~2
```

Phir:

```bash
git push origin main --force
```

Ab latest commit hoga:

```text
1ad3f41
```

---

# 9. Reset karne se pehle Backup banana — Recommended

Agar beginner ho, directly reset mat karo.

Pehle backup branch banao:

```bash
git branch backup-before-reset
```

Ab tumhare paas backup hai.

Phir:

```bash
git reset --hard HEAD~2
```

Aur:

```bash
git push origin main --force
```

Agar baad me purani state wapas chahiye:

```bash
git reset --hard backup-before-reset
```

Phir:

```bash
git push origin main --force
```

---

# 10. `git reset` ke 3 important types

## `--soft`

```bash
git reset --soft HEAD~1
```

Commit remove hota hai.

Lekin changes staged rehte hain.

Useful jab:

> Commit galat message ke saath ban gaya aur mujhe changes dobara commit karne hain.

---

## `--mixed`

```bash
git reset HEAD~1
```

Ye default reset hai.

Commit remove hota hai aur changes working directory me rehte hain, lekin staged nahi rehte.

---

## `--hard`

```bash
git reset --hard HEAD~1
```

Commit + changes dono remove.

Use carefully.

---

# 11. `git revert` — Safe Way

Agar commit GitHub par already push ho chuka hai aur doosre developers bhi us branch ko use kar rahe hain, generally history rewrite karne ke bajay `revert` safer hota hai.

Example:

```text
A → B → C
```

C galat hai.

Use:

```bash
git revert C
```

Git ek **new commit** create karega jo C ke changes ko undo karega.

Result:

```text
A → B → C → D
```

D = C ko undo karne wala commit.

### Important Difference

`reset`:

```text
A → B → C

reset


A → B
```

`revert`:

```text
A → B → C

revert

A → B → C → D
```

---

# 12. Reset vs Revert

| Situation                                                    | Command                  |
| ------------------------------------------------------------ | ------------------------ |
| Local commit galat                                           | `git reset`              |
| GitHub commit ko history se hatana hai                       | `git reset` + force push |
| Shared/public branch                                         | Usually `git revert`     |
| Commit ka code undo karna hai but history preserve karni hai | `git revert`             |
| Commit ko completely history se remove karna hai             | `git reset`              |
| Beginner ho aur unsure ho                                    | Backup branch first      |

---

# 13. Force Push kya hai?

Normal:

```bash
git push origin main
```

Force:

```bash
git push origin main --force
```

Force push remote branch ki history ko local history ke according overwrite karta hai.

### ⚠️ Dangerous

Agar doosre developers same branch par kaam kar rahe hain, force push unki history ko disturb kar sakta hai.

Isliye possible ho to:

```bash
git push origin main --force-with-lease
```

use karna better hai.

`--force-with-lease` check karta hai ki remote branch unexpectedly change to nahi hui.

---

# 14. `--force` vs `--force-with-lease`

### Force

```bash
git push origin main --force
```

Remote history ko forcefully overwrite karega.

### Force with lease

```bash
git push origin main --force-with-lease
```

Agar remote me kisi aur ka unexpected change hai to push reject kar sakta hai.

### Recommendation

Agar force push zaroori hai:

```bash
git push origin main --force-with-lease
```

prefer karo.

---

# 15. Agar Commit Delete Kar Diya To Wapas Kaise Laayen?

Git me `reflog` bahut useful hai.

```bash
git reflog
```

Example:

```text
1ad3f41 HEAD@{0}: reset: moving to HEAD~2
98215a7 HEAD@{1}: commit: Implement feature X
d41c989 HEAD@{2}: commit: .
```

Agar `98215a7` wapas chahiye:

```bash
git reset --hard 98215a7
```

Phir:

```bash
git push origin main --force
```

---

# 16. Reflog kya hai?

`git reflog` Git ke HEAD movement ki history rakhta hai.

Useful when:

* Galti se reset kar diya
* Commit delete kar diya
* Wrong branch par reset ho gaya
* Previous state recover karni hai

Command:

```bash
git reflog
```

---

# 17. Galti se `git reset --hard` kar diya

Panic mat karo.

Sabse pehle:

```bash
git reflog
```

Previous commit identify karo.

Example:

```text
98215a7 HEAD@{1}
```

Then:

```bash
git reset --hard 98215a7
```

Agar remote update karna hai:

```bash
git push origin main --force-with-lease
```

---

# 18. Specific Commit par jaana

Agar specific commit par temporarily jaana hai:

```bash
git checkout 98215a7
```

Modern Git me:

```bash
git switch --detach 98215a7
```

Is state me tum **detached HEAD** par hoge.

Agar isi state se kaam continue karna hai to branch banao:

```bash
git switch -c recovery-branch
```

---

# 19. Current Branch Check Karna

```bash
git branch
```

Example:

```text
* main
  development
  feature/login
```

`*` current branch ko show karta hai.

---

# 20. Branch Create Karna

```bash
git branch feature/login
```

Ya directly create + switch:

```bash
git switch -c feature/login
```

---

# 21. Branch Change Karna

```bash
git switch main
```

Old command:

```bash
git checkout main
```

---

# 22. Branch Delete Karna

Local branch:

```bash
git branch -d feature/login
```

Force delete:

```bash
git branch -D feature/login
```

Remote branch:

```bash
git push origin --delete feature/login
```

---

# 23. New Branch ko GitHub par Push Karna

```bash
git switch -c feature/login
```

Code changes karo.

```bash
git add .
git commit -m "Add login feature"
```

Push:

```bash
git push -u origin feature/login
```

---

# 24. GitHub se Latest Changes Lana

```bash
git pull origin main
```

Ye generally:

```text
git fetch
+
git merge
```

jaisa kaam karta hai.

---

# 25. Sirf Remote Changes Check Karna

```bash
git fetch origin
```

Ye remote changes download karta hai, lekin current branch me merge nahi karta.

Uske baad:

```bash
git log --oneline HEAD..origin/main
```

Remote par naye commits dekh sakte ho.

---

# 26. `git pull` se Conflict aa gaya

Example:

```text
<<<<<<< HEAD

Your code

=======

Remote code

>>>>>>> origin/main
```

Tumhe manually decide karna hota hai ki kaunsa code rakhna hai.

Conflict fix karne ke baad:

```bash
git add .
```

Then:

```bash
git commit
```

Ya agar Git merge commit message deta hai to save karke commit complete karo.

---

# 27. Local Changes ko Temporarily Save Karna

Agar tumne code change kiya hai lekin commit nahi karna aur branch change karni hai:

```bash
git stash
```

Phir branch change:

```bash
git switch main
```

Baad me changes wapas:

```bash
git stash pop
```

---

# 28. Multiple Stashes

Stashes dekhne ke liye:

```bash
git stash list
```

Example:

```text
stash@{0}
stash@{1}
stash@{2}
```

Specific stash apply:

```bash
git stash apply stash@{1}
```

---

# 29. Last Commit ka Message Change Karna

Agar latest commit GitHub par push nahi hua:

```bash
git commit --amend -m "Correct commit message"
```

Agar already GitHub par push kar diya hai:

```bash
git push --force-with-lease
```

---

# 30. Last Commit me Extra Changes Add Karna

Pehle changes:

```bash
git add .
```

Then:

```bash
git commit --amend --no-edit
```

Ye latest commit me new changes add karega.

Agar already pushed hai:

```bash
git push --force-with-lease
```

---

# 31. Accidentally Wrong Files Commit Kar Di

Agar commit abhi push nahi hua:

```bash
git reset --soft HEAD~1
```

Ab files staging area me rahengi.

Wrong file ko unstage:

```bash
git restore --staged filename
```

Phir correct files:

```bash
git add .
git commit -m "Correct commit"
```

---

# 32. GitHub par Galat Commit Push Ho Gaya

## Case A — Sirf tum kaam kar rahe ho

Agar latest commit completely remove karna hai:

```bash
git reset --hard HEAD~1
git push origin main --force-with-lease
```

---

## Case B — Shared branch hai

Prefer:

```bash
git revert HEAD
```

Then:

```bash
git push origin main
```

---

# 33. Galti Se Sensitive Information Push Ho Gayi

Example:

```text
.env
API key
Database password
Private key
Secret token
```

Sirf Git history se file delete karna enough nahi hota.

### Immediately:

1. Secret/API key rotate karo.
2. Password change karo.
3. Token revoke karo.
4. `.gitignore` update karo.
5. Git history clean karo if required.

`.env` ko `.gitignore` me rakho:

```gitignore
.env
.env.local
.env.production
```

### Important

Agar secret GitHub par push ho gaya hai, **sirf commit delete karna security fix nahi hai**. Secret ko invalidate/rotate karna zaroori hai.

---

# 34. `.gitignore`

Example:

```gitignore
node_modules/
.env
.env.local
dist/
build/
.DS_Store
```

Git me unnecessary files commit hone se bachata hai.

---

# 35. Remote URL Check Karna

```bash
git remote -v
```

Example:

```text
origin  https://github.com/username/project.git (fetch)
origin  https://github.com/username/project.git (push)
```

---

# 36. Remote URL Change Karna

```bash
git remote set-url origin https://github.com/username/new-repo.git
```

Check:

```bash
git remote -v
```

---

# 37. New Project ko GitHub par Push Karna

Project folder me:

```bash
git init
```

Files:

```bash
git add .
```

Commit:

```bash
git commit -m "Initial commit"
```

Remote:

```bash
git remote add origin https://github.com/username/project.git
```

Branch:

```bash
git branch -M main
```

Push:

```bash
git push -u origin main
```

---

# 38. GitHub Repository Clone Karna

```bash
git clone https://github.com/username/project.git
```

Folder me:

```bash
cd project
```

Dependencies:

```bash
npm install
```

---

# 39. Changes Compare Karna

Unstaged changes:

```bash
git diff
```

Staged changes:

```bash
git diff --staged
```

Specific commit compare:

```bash
git diff 98215a7 1ad3f41
```

---

# 40. File ke Changes Undo Karna

Agar file modify ki hai lekin changes discard karne hain:

```bash
git restore filename
```

All unstaged changes:

```bash
git restore .
```

### ⚠️

Ye uncommitted changes permanently remove kar sakta hai.

---

# 41. Staged File ko Unstage Karna

```bash
git restore --staged filename
```

All staged files:

```bash
git restore --staged .
```

---

# 42. Commit ko Delete Nahi Karna, Sirf Changes Undo Karne Hain

Use:

```bash
git revert <commit-hash>
```

Example:

```bash
git revert 98215a7
```

Then:

```bash
git push origin main
```

---

# 43. Kisi Commit ka Code Dekhna

```bash
git show 98215a7
```

Sirf commit information:

```bash
git show --stat 98215a7
```

---

# 44. Do Commits ke Beech Difference

```bash
git diff 1ad3f41 98215a7
```

---

# 45. Current Commit Check Karna

```bash
git rev-parse HEAD
```

Short hash:

```bash
git rev-parse --short HEAD
```

---

# 46. Git Status Clean Hai Ya Nahi

```bash
git status
```

Agar:

```text
nothing to commit, working tree clean
```

to local working tree clean hai.

---

# 47. Daily Development Workflow

Recommended workflow:

```bash
git status
```

```bash
git pull origin main
```

Code changes karo.

```bash
git status
```

```bash
git add .
```

```bash
git commit -m "Add feature"
```

```bash
git push origin main
```

---

# 48. Feature Branch Workflow

Production projects me generally feature branch use karna better hota hai.

```text
main
 │
 ├── feature/login
 ├── feature/payment
 └── feature/dashboard
```

Example:

```bash
git switch main
git pull origin main
git switch -c feature/payment
```

Code karo:

```bash
git add .
git commit -m "Add payment integration"
```

Push:

```bash
git push -u origin feature/payment
```

Then GitHub par Pull Request create karo.

---

# 49. Main Branch ko Directly Modify Karne Se Bachna

Small personal project:

```text
main → commit → push
```

Team/production project:

```text
main
 ↓
feature branch
 ↓
commit
 ↓
push
 ↓
Pull Request
 ↓
Review
 ↓
Merge
 ↓
main
```

---

# 50. Most Important Commands Cheat Sheet

```bash
# Status
git status

# History
git log --oneline

# Detailed history
git log

# Add all
git add .

# Commit
git commit -m "message"

# Push
git push origin main

# Pull
git pull origin main

# Fetch
git fetch origin

# Current branch
git branch

# Create branch
git switch -c feature/name

# Switch branch
git switch main

# Delete local branch
git branch -d branch-name

# Stash
git stash

# Restore stash
git stash pop

# Revert commit
git revert <commit>

# Reset last commit but keep changes staged
git reset --soft HEAD~1

# Reset last commit and keep changes unstaged
git reset HEAD~1

# Completely remove last commit + changes
git reset --hard HEAD~1

# Recover previous states
git reflog

# Force push safely
git push origin main --force-with-lease
```

---

# 51. Decision Table — Kis Situation Me Kya Kare?

| Situation                               | Command                                   |
| --------------------------------------- | ----------------------------------------- |
| Changes check karne hain                | `git status`                              |
| History dekhni hai                      | `git log --oneline`                       |
| Changes Git me add karne hain           | `git add .`                               |
| Commit banana hai                       | `git commit -m "message"`                 |
| GitHub par push                         | `git push origin main`                    |
| Latest GitHub changes lana              | `git pull origin main`                    |
| Commit undo karna hai, history preserve | `git revert <hash>`                       |
| Local latest commit remove              | `git reset --soft HEAD~1`                 |
| Latest commit + changes remove          | `git reset --hard HEAD~1`                 |
| Latest 2 commits remove                 | `git reset --hard HEAD~2`                 |
| Reset ke baad GitHub update             | `git push origin main --force-with-lease` |
| Deleted commit recover                  | `git reflog`                              |
| Temporary changes save                  | `git stash`                               |
| Wrong commit message                    | `git commit --amend`                      |
| Branch create                           | `git switch -c branch-name`               |
| Branch change                           | `git switch branch-name`                  |
| Remote changes check                    | `git fetch origin`                        |
| Changes compare                         | `git diff`                                |

---

# 52. Golden Rules

## Rule 1 — Force Push Carefully

```bash
git push --force
```

ko casually use mat karo.

Prefer:

```bash
git push --force-with-lease
```

---

## Rule 2 — Shared Branch par Reset Avoid Karo

Agar multiple developers `main` use kar rahe hain:

```bash
git revert
```

generally safer option hai.

---

## Rule 3 — Reset se Pehle Backup

Agar unsure ho:

```bash
git branch backup-before-reset
```

Phir reset karo.

---

## Rule 4 — Reflog Yaad Rakho

Agar Git me kuch galat kar diya:

```bash
git reflog
```

pehle check karo.

---

## Rule 5 — Secret Commit Ho Gaya To Secret Rotate Karo

API key/password/token ko sirf Git history se delete karna enough nahi hai.

---

# 53. Beginner ke Liye Emergency Procedure

Agar tumhe lagta hai:

> "Bhai maine Git me kuch galat kar diya hai."

To random commands mat chalana.

### Step 1

```bash
git status
```

### Step 2

```bash
git log --oneline -10
```

### Step 3

Agar reset/rebase/cherry-pick wagaira kiya hai:

```bash
git reflog
```

### Step 4

Agar unsure ho, current state ka backup:

```bash
git branch emergency-backup
```

### Step 5

Uske baad hi recovery command decide karo.

---

# 54. Tumhare Current Example ka Complete Solution

Tumhari original history:

```text
98215a7 Implement feature X
d41c989 .
1ad3f41 fix: Rename startData to startDate
41cde3c fix: Correct typo
95d0cfb feat: Add remaining balance
```

Goal:

```text
98215a7 ❌ Remove
d41c989 ❌ Remove

1ad3f41 ✅ Keep
41cde3c ✅ Keep
95d0cfb ✅ Keep
```

### Safest approach

Backup:

```bash
git branch backup-before-reset
```

Reset:

```bash
git reset --hard HEAD~2
```

Push:

```bash
git push origin main --force-with-lease
```

Result:

```text
1ad3f41
41cde3c
95d0cfb
```

### Agar baad me dono commits wapas chahiye

```bash
git reset --hard backup-before-reset
```

Then:

```bash
git push origin main --force-with-lease
```

---

# 55. Final Mental Model

Git ko is tarah samjho:

```text
Working Directory
       ↓
    git add
       ↓
Staging Area
       ↓
  git commit
       ↓
Local Repository
       ↓
  git push
       ↓
     GitHub
```

Aur undo/recovery:

```text
git restore
    ↓
File changes undo


git reset
    ↓
Commit history move/remove


git revert
    ↓
New commit se old changes undo


git reflog
    ↓
Lost commit/state recover
```

### Sabse important 4 commands

Agar beginner ho to pehle in 4 ko properly samjho:

```bash
git status
git log --oneline
git reset
git revert
```

Aur recovery ke liye:

```bash
git reflog
```

**Golden principle:**

> Git me kuch bhi destructive karne se pehle backup branch bana lo.

```bash
git branch backup-before-change
```

Phir experiment karo.
