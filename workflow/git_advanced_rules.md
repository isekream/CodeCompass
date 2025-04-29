# Git (Advanced) Rules

## Commits & History Management

* **Atomic Commits:** Each commit should represent a single logical change (e.g., fix a specific bug, implement a small feature part, refactor a specific module). Avoid large, unrelated changes in a single commit.
* **Commit Messages:**
    * Follow the Conventional Commits specification (`<type>(<scope>): <description>`) for consistency and to enable automated tooling (e.g., changelog generation).
    * Write clear, concise subject lines (imperative mood, e.g., "Fix login bug", not "Fixed login bug"). Aim for <= 50 characters.
    * Use the commit body to explain the *why* and *how* of the change if the subject line isn't sufficient.
* **Clean History:** Aim for a clean, linear, and understandable history on main integration branches (e.g., `main`, `develop`). Use tools like interactive rebase (`git rebase -i`) on *local* feature branches before merging/creating PRs to:
    * `squash` / `fixup`: Combine small, related work-in-progress commits into logical units.
    * `reword`: Improve commit messages.
    * `reorder`: Arrange commits logically.
    * `drop`: Remove unnecessary commits.
* **Sign Commits (Optional but Recommended):** Consider signing commits (e.g., using GPG keys) to verify authorship and integrity, especially in security-sensitive projects or open-source contributions.

## Branching Strategies

* **Choose Appropriately:** Select a branching strategy that fits the team size, project complexity, and release cadence (e.g., GitHub Flow, GitLab Flow, Gitflow, Trunk-Based Development). Document the chosen strategy.
* **Feature Branches:** Develop new features and bug fixes on separate branches (feature branches) for isolation. Keep feature branches short-lived to minimize divergence from the main line and reduce merge conflicts.
* **Branch Naming:** Use consistent and descriptive branch names (e.g., `feat/add-user-auth`, `fix/login-bug-123`, `chore/update-dependencies`). Include issue tracker IDs if applicable.
* **Main Branch Protection:** Protect main integration branches (`main`, `develop`) against direct pushes. Require Pull Requests/Merge Requests with code reviews and passing CI checks.

## Rebasing vs. Merging

* **Rebase for Local Cleanup:** Use `git rebase -i` (interactive rebase) on your *local* feature branches *before* sharing them (pushing/creating PR) to clean up your commit history (squash, fixup, reword).
* **Rebase for Updates:** Use `git pull --rebase` (or `git fetch` followed by `git rebase origin/main`) on your feature branch to incorporate updates from the main branch *before* merging. This maintains a linear history for the feature branch itself, making the final merge cleaner.
* **Merge for Integration:** Use standard merge commits (`git merge --no-ff`) when integrating completed and reviewed feature branches into main integration branches (`main`, `develop`). This preserves the context of the feature branch and makes rollbacks simpler if needed. Avoid fast-forward merges on these main branches to retain branch history.
* **NEVER Rebase Shared History:** **Do NOT rebase branches that have already been pushed and are being used by others** (e.g., `main`, `develop`, shared feature branches). Rebasing rewrites history, which causes major problems for collaborators who have based work on the original history.

## Pull Requests / Merge Requests (PRs/MRs)

* **Clear Descriptions:** Write clear PR/MR titles and descriptions explaining the purpose of the change, linking to relevant issues, and providing context for reviewers.
* **Focused PRs:** Keep PRs small and focused on a single feature or fix. Large PRs are difficult and time-consuming to review effectively.
* **Code Review:** Require at least one approval (or more, depending on team policy) from peers before merging. Address all review comments.
* **CI Checks:** Ensure all automated checks (linting, tests, security scans) pass before merging.

## Advanced Commands & Techniques

* **`git stash`:** Use `git stash` to temporarily save uncommitted changes (staged and unstaged) when you need to switch branches quickly without committing incomplete work. Use `git stash pop` or `git stash apply` to reapply changes later. Consider `git stash save "message"` for clarity when using multiple stashes.
* **`git cherry-pick`:** Use `git cherry-pick <commit-hash>` to apply specific commits from one branch onto another. Use cases include backporting bug fixes to release branches or applying a specific hotfix without merging an entire feature branch. Use with caution as it duplicates commits and can make history harder to follow if overused. Prefer merging or rebasing where possible.
* **`git bisect`:** Use `git bisect` to efficiently find the specific commit that introduced a bug by performing a binary search through the commit history. Mark commits as `good` or `bad` until the faulty commit is isolated.
* **`git reflog`:** Use `git reflog` to view a log of where `HEAD` and branch references have pointed. Useful for recovering lost commits or undoing mistakes (e.g., after a bad rebase).
* **Interactive Add (`git add -p`):** Use `git add -p` (patch mode) to stage only specific chunks of changes within files, allowing for more granular, atomic commits.

## Git Hooks

* **Leverage Hooks:** Utilize Git hooks (scripts triggered by Git events like `pre-commit`, `pre-push`, `commit-msg`) to automate checks and enforce standards locally or server-side.
* **Client-Side Hooks (`pre-commit`, `prepare-commit-msg`, `commit-msg`, `pre-push`):**
    * Use `pre-commit` hooks for running linters, formatters, and fast unit tests before allowing a commit.
    * Use `commit-msg` hooks to validate commit message formats (e.g., Conventional Commits).
    * Use `pre-push` hooks to run integration tests or other checks before code leaves the local machine.
* **Server-Side Hooks (`pre-receive`, `update`, `post-receive`):** Use server-side hooks (if supported by the hosting platform or self-hosted server) to enforce project policies, trigger deployments, or send notifications.
* **Management:** Use hook management frameworks (e.g., `pre-commit`, `husky`, `lefthook`) to simplify sharing and managing hooks across a team, rather than requiring manual setup in `.git/hooks/`.
* **Keep Hooks Fast:** Client-side hooks, especially `pre-commit`, should run quickly to avoid disrupting the developer workflow.

## Git Large File Storage (LFS)

* **Use for Large Binaries:** Use Git LFS (`git lfs`) to track large binary files (e.g., images, videos, audio, datasets, compiled artifacts) in Git repositories without bloating the repository history.
* **Track Selectively:** Configure Git LFS using `git lfs track "*.psd"` (stored in `.gitattributes`) to track only specific file types or paths that are genuinely large and binary. Do not track code or small text files with LFS.
* **Installation:** Ensure all team members collaborating on the repository have Git LFS installed (`git lfs install`).
* **Workflow:** The standard `git add`, `git commit`, `git push`, `git pull` commands work mostly transparently, but LFS uploads/downloads happen separately. Large files are downloaded lazily on checkout.
* **Pruning:** Regularly run `git lfs prune` locally to remove old, unreferenced LFS files from the local cache and free up disk space.
* **Server Storage:** Be mindful of LFS storage quotas and costs on Git hosting platforms (GitHub, GitLab, Bitbucket) or self-hosted LFS servers.
* **Security:** Ensure access control is properly configured for both the Git repository and the LFS storage backend. Use encrypted connections (HTTPS/SSH) for LFS transfers.

## Git Performance & Repository Health

* **Avoid Large Repositories:** Keep repositories reasonably sized by:
  * Using Git LFS for large binary files.
  * Separating unrelated projects into different repositories.
  * Using submodules or package managers for shared code dependencies.
  
* **Repository Maintenance:**
  * Occasionally run `git gc` to clean up and optimize the local repository.
  * Use `git prune` to remove unreachable objects.
  * Consider running `git fsck` to check repository integrity.
  
* **Shallow Clones (when appropriate):**
  * For CI/CD pipelines or environments where full history isn't needed, use `git clone --depth=1` for faster clones.
  * Use `git fetch --unshallow` later if full history is needed.

## Collaboration & Etiquette

* **Keep Everyone Updated:** Communicate significant branch creations, merges, or rebases to affected team members.
* **Respect Branch Ownership:** Coordinate with the primary author before making changes to others' feature branches.
* **Documentation:** Maintain documentation on the team's Git workflow, branching strategy, and commit conventions.
* **Stay Current:** Keep Git clients and tools updated for security patches and new features.
* **Respect CI/CD Resources:** Be mindful of triggering unnecessary CI/CD pipelines with frequent pushes or large changes.
