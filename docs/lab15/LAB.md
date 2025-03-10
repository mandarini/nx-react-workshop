# 💎 Lab 15 - Setting up CI

###### ⏰ &nbsp;Estimated time: 5-10 minutes

## 📚 Learning outcomes:

- Basics of GitHub actions
- Use Nx to setup scalable checks on your PRs to ensure only passing code goes into master
- Explore other practical uses of `nx affected`

## 🏋️‍♀️ Steps :

Before starting on this lab, it's important that you have a version of your local workshop
pushed to your GitHub repo.

1.  Let's make sure the master branch is up to date (it's important your latest changes are on `main` for the follow-up steps): - If you already are on `main` - commit everything:
    `git add . && git commit -m "finished lab 14" git push origin master`

        > If you are on a different branch, commit everything, switch to master

        and bring it up to date:

        ```shell
        git add . && git commit "finish lab 14"
        git checkout master
        git merge previous-branch-you-were-on
        git push origin master
        ```

    <br />

2.  Create a new file `.github/workflows/ci.yml`

        ```yml
        name: Run CI checks # The name will show up on the GitHub Actions dashboard

        on: [pull_request] # This workflow will run only on Pull Requests

        jobs:
          test-store: # give our job an ID
            runs-on: ubuntu-latest # the image our job will run on
            name: Test Store # the name that will appear on the Actions UI
            steps: # what steps it will perform
              - uses: actions/checkout@v4 # checkout whatever branch the PR is using
              - uses: bahmutov/npm-install@v1.4.5 # trigger an `npm install`
              - run: npm run nx test store # test the "store" project
          test-api:
            runs-on: ubuntu-latest
            name: Test API
            steps:
              - uses: actions/checkout@v4
              - uses: bahmutov/npm-install@v1.4.5
              - run: npm run nx test api
        ```

    <br />

3.  Commit and then switch to a new branch:

        ```
        git add . && git commit -m "add ci"
        git push origin master
        git checkout -b dynamic-title
        ```

        ⚠️&nbsp;&nbsp;I know we **just** switched to master above. But it was important we bring it
        up to date. Now we need to switch to a new branch so we can submit our PR.

    <br />

4.  Open `apps/store/src/app/app.tsx`
    <br />

5.  And make _any change at all to the component_ (the title of the header dynamic):

    ```
    const title = "Board Game Hoard";
    // ...
    <Header title={title} />
    ```

    <details>
    <summary>🐳 &nbsp;&nbsp;Hint - set the props in the Header as well </summary>

    ```tsx
    export interface HeaderProps {
      title: string;
    }

    export const Header = ({ title }: HeaderProps) => {
      return (
        <AppBar position="static">
          <Toolbar>
            <Typography variant="h6" sx={{ flexGrow: 1 }}>
              {title}
            </Typography>
          </Toolbar>
        </AppBar>
      );
    };
    ```

    </details><br />

6.  Commit all your changes and push your new branch.
    <br />

7.  Go to GitHub and make a Pull Request to `main`
    <br />

8.  After a few moments you'll see something like this:
    ![GitHub Actions example](./github_actions.png)
    <br />

9.  The unit tests will be failing - that's expected.
    <br />

---

We are starting to set-up our CI, that will verify our Pull Requests to ensure bad code
doesn't go into `main`.

But now we're testing both projects - even though we only changed the store.

---

### Testing only affected

10. Let's use `nx affected` to only test the changed projects:

        Instead of running two `nx` commands in your CI, run a single `nx affected` command
        that tests all affected projects.

        <details>
        <summary>🐳 &nbsp;&nbsp;Hint 1</summary>

        Refer to the [docs](https://nx.dev/nx-api/nx/documents/affected)
        </details>

        <details>
        <summary>🐳 &nbsp;&nbsp;Hint 2</summary>

        Since it's a Pull Request, your base commit will always be `--base=origin/main`
        </details>

        <details>
        <summary>🐳 &nbsp;&nbsp;Hint 3</summary>

        You should only need 1 job now:

        ```yaml
        jobs:
          test:
            runs-on: ubuntu-latest
            name: Testing affected apps
            steps:
              - uses: actions/checkout@v4
              - uses: bahmutov/npm-install@v1.4.5
              - run: .....
        ```

        </details>

        ⚠️&nbsp;&nbsp;It's okay to work on this on your new branch. We'll merge everything to `main`
        eventually.

    <br />

11. Commit and push. On your Github Actions log you should see only the `store` tests running:

    <img src="./store_affected.png" width="500" alt="Only store tests are running"><br />

12. Our tests are now being ran sequentially for each project. See if you can run them in parallel (consult the Nx Affected [docs](https://nx.dev/nx-api/nx/documents/affected) if unsure)
    <br />

13. Our CI only does testing now. But we also have targets for `lint`, `e2e` and `build`. Would really be handy if CI also told us if any of those failed.

        **Add more jobs under your CI workflow that run affected for each of the above targets**

    <br />

14. Commit and push your `ci.yml` changes.
    <br />

15. You'll notice some new steps in the GitHub Actions UI. Some of them are failing. That is okay. We can fix them later.
    <br />

16. For now, you can merge your PR into `main `
    <br />

17. Switch to `main` locally and pull latest so all your new CI changes are up to date.

        ```shell
        git checkout master
        git pull origin master
        ```

    <br />

18. **BONUS:** Currently, if we create a PR with a change **only** to our `ci.yml` file, our `nx affected` commands won't run at all: as they'll think no project has been affected:

    <img src="./no_affected.png" width="500" alt="Changes to ci.yml does not cause anything to be affected">

    To be safe, we'd like to mark all projects as affected whenever we change our CI config, as we don't know what those changes could have broken.
    Have a look through the docs in the hint and see if you can do this.

    <details>
    <summary>🐳 &nbsp;&nbsp;Hint</summary>

    [Setting named inputs](https://nx.dev/reference/nx-json#inputs-namedinputs)
    </details><br />

---

🎓&nbsp;&nbsp;If you get stuck, check out [the solution](SOLUTION.md)

---

[➡️ &nbsp;Next lab ➡️](../lab16/LAB.md)
