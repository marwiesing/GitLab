To add the new submodule `Demo_Project` to your repository, follow these steps:

### 1. Add the New Submodule
Run the following command to add the new submodule:

```bash
git submodule add git@gitlab.com:udemy7523451/demo_project.git section08_Start_Demo_Project/Demo_Project
```

This will:
- Clone the new submodule repository into the specified path (`section08_Start_Demo_Project/Demo_Project`).
- Update your `.gitmodules` file with the new submodule information.

### 2. Verify `.gitmodules`
Check that the `.gitmodules` file has been updated correctly:

```bash
cat .gitmodules
```

You should now see an entry like this:

```plaintext
[submodule "section03_Pipeline/gitlab-subrepo"]
        path = section03_Pipeline/gitlab-subrepo
        url = git@gitlab.com:udemy7523451/my-first-project.git
[submodule "section04_Artifacts/artifacts_demo"]
        path = section04_Artifacts/artifacts_demo
        url = git@gitlab.com:udemy7523451/artifacts_demo.git
[submodule "section06_Local_Runners/local-runners"]
        path = section06_Local_Runners/local-runners
        url = git@gitlab.com:udemy7523451/runners-project.git
[submodule "section07_Variables/Variables_Demo"]
        path = section07_Variables/Variables_Demo
        url = git@gitlab.com:udemy7523451/variables_demo.git
[submodule "section08_Start_Demo_Project/Demo_Project"]
        path = section08_Start_Demo_Project/Demo_Project
        url = git@gitlab.com:udemy7523451/demo_project.git
```

### 3. Initialize and Update the Submodule
After adding the submodule, initialize and update it:

```bash
git submodule update --init --recursive
```

### 4. Commit the Changes
Stage and commit the updated `.gitmodules` file and the submodule changes:

```bash
git add .gitmodules section08_Start_Demo_Project/Demo_Project
git commit -m "Add submodule: Demo_Project"
```

### 5. Push Changes to the Main Repository
Finally, push your changes to the main repository:

```bash
git push origin main
```

Your new submodule `Demo_Project` is now added and configured.