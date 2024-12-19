### Cheat Sheet: GitHub vs. GitLab Commands for Common Git Operations

GitHub and GitLab both use Git, so most commands are identical. However, there are some differences in the workflows and additional features they support. Here's a breakdown:

---

### **Common Git Commands (Same for GitHub and GitLab)**
1. **Clone a Repository**
   ```bash
   git clone <repository-url>
   ```
   - Example:
     - GitHub: `git clone git@github.com:user/repo.git`
     - GitLab: `git clone git@gitlab.com:user/repo.git`

2. **Add and Commit Changes**
   ```bash
   git add .
   git commit -m "Your commit message"
   ```

3. **Push Changes**
   ```bash
   git push origin <branch-name>
   ```
   - Example: `git push origin main`

4. **Pull Changes**
   ```bash
   git pull origin <branch-name>
   ```
   - Example: `git pull origin main`

5. **Check Out a Branch**
   ```bash
   git checkout <branch-name>
   ```

6. **Create a New Branch**
   ```bash
   git checkout -b <new-branch-name>
   ```

7. **View Repository Status**
   ```bash
   git status
   ```

8. **View Commit Logs**
   ```bash
   git log
   ```

9. **Merge Branches**
   ```bash
   git merge <branch-name>
   ```

---

### **GitLab-Specific Features**
These commands are unique to GitLab's extended features:

1. **Pull and Push with Specific SSH Keys**
   - If you've configured a unique SSH key for GitLab:
     ```bash
     GIT_SSH_COMMAND='ssh -i ~/.ssh/id_ed25519_gitlab' git push origin main
     ```

2. **GitLab CI/CD Integration**
   - Interact with GitLab's pipelines:
     ```bash
     gitlab-runner register
     ```

3. **Push to Protected Branches**
   - If protected branches are enforced in GitLab, you need specific permissions.

---

### **GitHub-Specific Features**
These commands are unique to GitHub's workflows:

1. **GitHub CLI Integration**
   - Install GitHub CLI: [GitHub CLI](https://cli.github.com/)
   - Clone a repository using GitHub CLI:
     ```bash
     gh repo clone user/repo
     ```
   - Create a pull request:
     ```bash
     gh pr create --title "Title" --body "Description"
     ```

2. **GitHub Actions Integration**
   - Interact with GitHub Actions workflows directly from the repository interface.

---

### **Key Differences**
| Feature                  | GitHub Command                 | GitLab Command                 |
|--------------------------|--------------------------------|--------------------------------|
| Repository Clone         | `git clone <github-url>`      | `git clone <gitlab-url>`      |
| Push Changes             | `git push`                    | `git push`                    |
| Protected Branches       | Permissions required          | Permissions required          |
| CI/CD Pipelines          | GitHub Actions (`.github/`)   | GitLab CI (`.gitlab-ci.yml`)  |
| Pull Request/Merge Request | Create in Web Interface or CLI (`gh`) | Create in Web Interface      |

---

### Summary
For basic Git operations like clone, pull, push, and checkout, the commands are identical between GitHub and GitLab. The differences emerge in platform-specific features like CI/CD pipelines or the use of GitHub CLI vs. GitLab runners.