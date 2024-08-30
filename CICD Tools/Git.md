# Git

- [Git](#git)
- [git rebase and git merge with --ff-only](#git-rebase-and-git-merge-with---ff-only)
- [what happens when you git merge a branch into another branch](#what-happens-when-you-git-merge-a-branch-into-another-branch)
- [Git Credentials](#git-credentials)
- [Git Internals - Environment Variables](#git-internals---environment-variables)
- [References](#references)

# git rebase and git merge with --ff-only

Both git rebase and git merge are used to integrate changes from one branch into another in Git. However, they differ in their approach and impact:

**Git rebase**:

- Rewrites history:
    Rebasing rewinds your current branch onto the target branch, replays your commits on top of the target branch's history, and creates new commits in the process. This effectively rewrites the history of your branch, resulting in a linear history where your commits appear directly after the target branch's commits.

- Flexibility:
    Offers more flexibility when rearranging or modifying commits before integrating them. You can squash multiple commits into one, edit commit messages, or even drop commits entirely.

- Potential conflicts:
    More prone to merge conflicts as it attempts to reapply your commits on top of a potentially changed baseline. Resolving these conflicts can be more complex.

- Public branches:
    Not recommended for public branches as it rewrites history that others might have already pulled or integrated.

**Git merge with --ff-only**:

- Fast-forward merge:
    When the current branch history is a direct linear descendant of the target branch (no new commits on your branch), a fast-forward merge simply moves the branch pointer to the target branch tip. No new commit is created, as your branch already reflects the changes.

- Simpler and cleaner:
    Creates a cleaner history without rewriting commits, making it ideal for public branches or maintaining clear provenance.

- Limited flexibility:
    Can only be used when a fast-forward merge is possible (linear history). If your branch has diverged from the target branch, it cannot be used, and a regular merge (with potential conflicts) is necessary.

Here's a table summarizing the key differences:

| Feature                    | git rebase                          | git merge (fast-forward)                 |
| :------------------------- | :---------------------------------- | :--------------------------------------- |
| Rewrites history           | Yes                                 | No                                       |
| Creates merge commit       | No                                  | Yes                                      |
| Potential conflicts        | Yes                                 | No                                       |
| Risky with public branches | Yes                                 | No                                       |
| Use case                   | Personal branches, cleaning history | Merging features, external contributions |

**Choosing the right option depends on your specific needs and workflow**:

Always consider your specific needs and the potential impact on collaborators before choosing either option.

- If you want a clean, linear history and don't mind potential conflicts (working on private branches), git rebase might be suitable.

- If you prioritize preserving history, avoiding conflicts, and maintaining clarity for collaborators, git merge (fast-forward) is the safer choice, especially for public branches.

- Use git rebase when you need flexibility in rearranging or modifying commits before integration, but be aware of potential conflicts and its unsuitability for public branches.

- Use git merge with --ff-only for clean and simple integration when your branch is a direct descendant of the target branch, especially on public branches.

Remember, understanding the differences between these commands and choosing the appropriate one will help you manage your Git workflow effectively.

---

***fatal: Need to specify how to reconcile divergent branches.***

The error message indicates that you're trying to run a command like git pull or git fetch without specifying how to handle the situation where your local branch has diverged from the remote branch you're trying to work with.

Here are the different ways you can resolve this issue:

1. Merge:

   - Use the git merge command followed by the branch name you want to merge into your current branch. This will combine the changes from both branches into your current branch, but it might create merge conflicts if there are overlapping changes.
   - You can use git merge --ff if the histories are linear and there are no conflicts. This will fast-forward your branch pointer to the tip of the remote branch without creating a new merge commit.

2. Rebase:

   - Use the git rebase command followed by the branch name you want to rebase your current branch on top of. This will rewrite your branch history to appear as if it was directly built on top of the remote branch, but it can be more complex to handle conflicts and potentially rewrite history that others might have already pulled.

3. Specify the strategy:

   - You can specify the strategy for reconciling branches with the -s flag on the git pull or git fetch command. Available strategies include merge, rebase, or ff-only. For example, git pull origin master -s merge tells Git to use the merge strategy for pulling from the master branch on the origin remote.

Here are some additional things to consider:

- Public branches: If you're working on a public branch, rebase is generally not recommended as it rewrites history that others might have already pulled. Choose merge or specify the ff-only strategy in such cases.
- Branch history: If your branch history is complex and you're unsure about the best approach, it's often safer to use the merge strategy as it avoids rewriting history.
- Conflicts: Both merge and rebase can lead to merge conflicts if there are overlapping changes. Be prepared to resolve them manually using a text editor.

Remember to choose the option that best suits your needs and workflow, considering the potential impact on collaborators and the complexity of your branch history.

---

***git pull : fatal: Need to specify how to reconcile divergent branches.***

This occurs because your local branch and the remote branch you're trying to pull from have diverged, meaning they have separate commits that haven't been merged or rebased. Git needs your direction on how to integrate these changes onto your branch.

Here are three ways to resolve this issue:

1. Merge:
   - Command: `git merge <remote_branch_name>` (e.g., git merge origin/master)
   - This integrates the remote branch's changes into your current branch by creating a new merge commit. However, it may lead to merge conflicts if there are overlapping changes. You'll need to manually resolve these conflicts before completing the merge.

2. Rebase:
   - Command: `git rebase <remote_branch_name>` (e.g., git rebase origin/master)
   - This rewrites your local branch history to appear as if it was built directly on top of the remote branch. This can be cleaner than merging, but it's important to avoid using it with public branches as it rewrites history that others might have already pulled. Be prepared to handle potential conflicts during the rebase process.

3. Specify strategy:
   - Command: `git pull <remote> <branch_name> -s <strategy>` (e.g., git pull origin master -s merge)
   - This lets you explicitly tell Git how to handle the divergent branches. Valid strategies include:
       - merge: Same as option 1 (creates a merge commit).
       - rebase: Same as option 2 (rewrites history).
       - ff-only: Only performs a pull if it can be done as a fast-forward (no merge or rebase needed, your branch is already up-to-date).

Choosing the right option:

- Public branches: Avoid rebase and stick to merge or ff-only to prevent rewriting history others might rely on.
- Confident in handling conflicts: Choose rebase if you're comfortable resolving potential conflicts and prefer a cleaner linear history.
- Unfamiliar with conflicts: Opt for merge to avoid rewriting history and simplify conflict resolution.

Remember to consider your specific situation and workflow when choosing an option. Consult your team's Git practices if unsure.

# what happens when you git merge a branch into another branch

**Purpose:**

- `git merge` integrates changes from one branch (the source branch) into another branch (the target branch), combining their development histories.
- It's commonly used to:
  - Incorporate feature work done on a feature branch into the main codebase (usually `master` or `main`).
  - Integrate changes from a remote repository (using `git pull`, which is essentially `git fetch` followed by `git merge`).

**Workflow:**

1. **Checkout target branch:** Use `git checkout <target_branch_name>` to switch to the branch where you want to integrate the changes.
2. **Run `git merge <source_branch_name>`:** This command initiates the merge process.
3. **Merge resolution (if necessary):**
    - **Clean merge:** If there are no conflicts (changes made to the same files in both branches), Git automatically integrates the changes, creating a new merge commit that points to the source and target branch commits.
    - **Merge conflict:** If there are conflicts (changes to the same lines within the same files), Git marks the conflicted files and pauses the merge, leaving you to manually resolve the conflicts by editing the files and adding a resolution commit.
4. **Push to remote (optional):** If you're working with a remote repository, use `git push` to propagate the merged changes to the remote branch.

**Key Concepts:**

- **Common ancestor:** The latest commit that both branches share before they diverged.
- **Three-way merge:** When Git encounters merge conflicts, it compares the common ancestor, the source branch's tip, and the target branch's tip to identify differences and guide your conflict resolution.
- **Merge commit:** A new commit created after a merge, incorporating the changes from both branches and referencing their parent commits.

**Additional Considerations:**

- Merging can be risky, so double-check your branches and resolve conflicts carefully before pushing to a remote repository.
- Use `git merge --no-ff` to force creation of a merge commit even when a fast-forward is possible, preserving history for traceability.
- Learn about advanced merge strategies (ours, recursive, etc.) for handling complex scenarios.

**Example:**

- You have a feature branch `new-feature` with changes to files `a.txt` and `b.txt`.
- You want to merge `new-feature` into the `master` branch.
- If there are no conflicts, `git merge new-feature` will create a new merge commit on `master`, incorporating the changes from `new-feature`.
- If there are conflicts, you'll need to resolve them manually before proceeding.

Git Environment variable GIT_MERGE_VERBOSITY controls the output for the recursive merge strategy. The allowed values are as follows:

- 0 outputs nothing, except possibly a single error message.
- 1 shows only conflicts.
- 2 also shows file changes.
- 3 shows when files are skipped because they haven’t changed.
- 4 shows all paths as they are processed.
- 5 and above show detailed debugging information.

The default value is 2

---

When merging a long-diverged feature branch with the main branch, avoiding conflicts entirely is difficult, but there are strategies to minimize them and make the process easier:

**Preventative Measures:**

1. **Rebase Early and Often:** Regularly rebase your feature branch onto the main branch to keep them in sync. This reduces the amount of change that needs to be merged at once, minimizing potential conflicts.
2. **Keep Feature Branches Small:** Break down large features into smaller, focused branches that are merged more frequently. This allows for easier conflict resolution and clearer history.
3. **Communicate with Team:** Discuss branching strategies and merge procedures with your team to ensure everyone is on the same page and potential conflicts are addressed early.

**Conflict Resolution Strategies:**

1. **Merge Conflicts are Inevitable:** Even with proactive measures, conflicts can still arise. Be prepared to manually resolve them using a text editor or Git's conflict resolution tools.
2. **Merge Strategically:** Consider using `git merge --ours` if you're confident your changes are important and the main branch can adapt. Use `git merge --theirs` if the main branch's changes are crucial.
3. **Rebase as an Alternative:** If conflicts seem complex or widespread, consider rebasing your feature branch onto the main branch. This rewrites history and integrates changes in a linear fashion, but can be risky if others have already pulled from the feature branch.

**Additional Tips:**

- **Use Clear Branch Names:** Descriptive branch names make it easier to understand what changes each branch contains and potential conflicts.
- **Write Good Commit Messages:** Detailed commit messages explaining changes and decisions help in conflict resolution and understanding the history.
- **Test Thoroughly:** Test your feature branch frequently, both before and after merging, to catch potential issues early and avoid introducing them to the main branch.

Remember, choosing the best approach depends on your specific situation and team workflow. Experiment and find what works best for you!

---

Here are three ways to get all the changes from the `main` branch into your feature branch and continue development while minimizing conflicts:

**1. Merge:**

- This is the standard approach, but it can potentially lead to conflicts if the branches have diverged significantly.
- **Steps:**
    1. Switch to your feature branch: `git checkout <feature_branch_name>`
    2. Fetch the latest changes from the remote repository: `git fetch origin`
    3. Merge the `main` branch into your feature branch: `git merge origin/main`
    4. Resolve any conflicts that arise manually.
- **Pros:** This is the simplest approach and preserves the history of your branch.
- **Cons:** Can be prone to conflicts, especially if the branches have diverged a lot.

**2. Rebase:**

- This rewrites the history of your feature branch to make it appear as if it was branched off the latest point in `main`.
- **Steps:**
    1. Switch to your feature branch: `git checkout <feature_branch_name>`
    2. Fetch the latest changes from the remote repository: `git fetch origin`
    3. Rebase your feature branch on top of `main`: `git rebase origin/main`
    4. Resolve any conflicts that arise manually.
- **Pros:** Less likely to have conflicts, especially if the branches have diverged significantly.
- **Cons:** Rewrites history, which can be confusing for others who have already pulled from your branch.

**3. Cherry-pick:**

- This allows you to selectively pick individual commits from the `main` branch and apply them to your feature branch.
- **Steps:**
    1. Switch to your feature branch: `git checkout <feature_branch_name>`
    2. Fetch the latest changes from the remote repository: `git fetch origin`
    3. List the commits in the `main` branch: `git log origin/main`
    4. Use `git cherry-pick` to add individual commits from `main` to your feature branch, resolving any conflicts manually.
- **Pros:** More control over which changes you integrate, useful if you only need specific changes from `main`.
- **Cons:** Can be tedious if you need to cherry-pick many commits. May create merge commits on your feature branch, making history less linear.

**Choosing the best approach:**

- If the branches have diverged a lot and you need all the changes from `main`, **rebase** or **cherry-pick** are safer choices to avoid conflicts.
- If the branches have not diverged much and you want to preserve the history of your branch, **merge** is a good option.
- Consider your team's workflow and preferences when choosing an approach.

**Additional tips:**

- Always test your feature branch after integrating changes from `main`.
- Communicate with your team about your chosen approach to avoid confusion.

---

# Git Credentials

Git will sometimes need credentials from the user in order to perform operations; for example, it may need to ask for a username and password in order to access a remote repository over HTTP. Some remotes accept a personal access token or OAuth access token as a password. This manual describes the mechanisms Git uses to request these credentials, as well as some features to avoid inputting these credentials repeatedly.

**REQUESTING CREDENTIALS**

Without any credential helpers defined, Git will try the following strategies to ask the user for usernames and passwords:

1) If the **GIT_ASKPASS** environment variable is set, the program specified by the variable is invoked. A suitable prompt is provided to the program on the command line, and the user’s input is read from its standard output.

```bash
GIT_ASKPASS=$(mktemp) && chmod a+rx $GIT_ASKPASS && export GIT_ASKPASS
cat > $GIT_ASKPASS <<< '#!/bin/bash
case "$1" in
    Username*) exec echo "$USERNAME" ;;
    Password*) exec echo "$PSW" ;;          
esac
.
```

2) Otherwise, if the **core.askPass** configuration variable is set, its value is used as above.
3) Otherwise, if the **SSH_ASKPASS** environment variable is set, its value is used as above.
4) Otherwise, the user is prompted on the terminal.


To set the SELinux context for the `GIT_ASKPASS` file, you can use the `chcon` command. Here’s a step-by-step guide:

1. **Identify the current SELinux context**:
   ```bash
   ls -Z /path/to/git-askpass
   ```

2. **Change the SELinux context**:
   ```bash
   sudo chcon -t bin_t /path/to/git-askpass
   ```
The `bin_t` type is just an example; you might need a different type depending on your system's policy.

3. **Make the change persistent**:
   ```bash
   sudo semanage fcontext -a -t bin_t /path/to/git-askpass
   sudo restorecon -v /path/to/git-askpass
   ```

These commands ensure that the SELinux context is correctly set and persists across reboots¹².

If you need more specific guidance based on your system's configuration, feel free to ask!

¹: [Red Hat Documentation](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/7/html/selinux_users_and_administrators_guide/sect-security-enhanced_linux-working_with_selinux-selinux_contexts_labeling_files)
²: [WhiteWinterWolf SELinux Cheatsheet](https://www.whitewinterwolf.com/posts/2017/09/08/selinux-cheatsheet/)

Source: Conversation with Copilot, 30/8/2024
(1) 4.7. SELinux Contexts – Labeling Files | Red Hat Product Documentation. https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/7/html/selinux_users_and_administrators_guide/sect-security-enhanced_linux-working_with_selinux-selinux_contexts_labeling_files.
(2) SELinux cheatsheet - WhiteWinterWolf.com. https://www.whitewinterwolf.com/posts/2017/09/08/selinux-cheatsheet/.
(3) Git Askpass for Windows | Git-Credential-Manager-for-Windows. https://microsoft.github.io/Git-Credential-Manager-for-Windows/Docs/Askpass.html.
# Git Internals - Environment Variables

Committing

| Environment Variable  | Description |
| :-------------------- | :---------- |
| **Committing**        | The final creation of a Git commit object is usually done by git-commit-tree, which uses these environment variables as its primary source of information, falling back to configuration values only if these aren’t present.
GIT_AUTHOR_NAME         | human-readable name in the “author” field.
GIT_AUTHOR_EMAIL        | email for the “author” field.
GIT_AUTHOR_DATE         | timestamp used for the “author” field.
GIT_COMMITTER_NAME      | sets the human name for the “committer” field.
GIT_COMMITTER_EMAIL     | email address for the “committer” field.
GIT_COMMITTER_DATE      | used for the timestamp in the “committer” field.
EMAIL                   | fallback email address in case the user.email configuration value isn’t set. If this isn’t set, Git falls back to the system user and host names.
**Networking**          |  
GIT_CURL_VERBOSE        | tells Git to emit all the messages generated by that library when Git uses the curl library to do network operations over HTTP
GIT_SSL_NO_VERIFY       | tells Git not to verify SSL certificates
GIT_HTTP_LOW_SPEED_LIMIT <br> GIT_HTTP_LOW_SPEED_TIME | If the data rate of an HTTP operation is lower than GIT_HTTP_LOW_SPEED_LIMIT bytes per second for longer than GIT_HTTP_LOW_SPEED_TIME seconds, Git will abort that operation. These values override the http.lowSpeedLimit and http.lowSpeedTime configuration values.
GIT_MERGE_VERBOSITY     | controls the output for the recursive merge strategy. Value Range from 0 to 6

# References

- [Git Internals - Environment Variables](https://git-scm.com/book/en/v2/Git-Internals-Environment-Variables)
