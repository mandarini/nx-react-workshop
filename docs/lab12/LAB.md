# 💡 Lab 12 - Module boundaries

###### ⏰ &nbsp;Estimated time: 10-15 minutes

## 📚 Learning outcomes:

- **Understand how to assign scopes and type tags to your libraries**
- **How to specify boundaries around your tags and avoid circular dependencies in your repo**
- **How to use linting to trigger warnings or errors when you are not respecting these boundaries**
  <br />

## 🏋️‍♀️ Steps :

1.  Open the `project.json` files for each project and **finish tagging the apps** accordingly:

    ```
      // apps/store/project.json
      {
        "projectType": "application",
        "root": "apps/store",
        "sourceRoot": "apps/store/src",
        "prefix": "bg-hoard",
        "targets": { ... },
        "tags": ["scope:store", "type:app"]
      }
    ```

    <br />

2.  Open the root `.eslintrc.json`, find the `"@nx/enforce-module-boundaries"` rule and **set the `depConstraints`**:

    ```
    "depConstraints": [
        {
            "sourceTag": "scope:store",
            "onlyDependOnLibsWithTags": ["scope:store", "scope:shared"]
        },
        .... <-- finish adding constraints for the tags we defined in the previous step
    ]
    ```

    <br />

3.  **Run `nx run-many --target=lint --all --parallel`**

    💡 `nx run-many` allows you run a specific target against a specific set of projects
    via the `--projects=[..]` option. However, you can also pass it the `--all` option
    to run that target against all projects in your workspace.

    💡 `--parallel` launches all the `lint` processes in parallel
    <br />

4.  We talked about how importing a **Feature** lib should not be allowed from a
    **UI** lib. Let's **test our lint rules** by doing just that: - In `libs/store/ui-shared/src/lib/header/header.tsx` - Try to `import { StoreFeatureGameDetail } from '@bg-hoard/store-feature-game-detail';`
    <br />

5.  **Run linting** against all the projects again.
    <br />

6.  You should see the expected error. Great! You can now **delete the import** above.
    <br />

7.  We also talked about the importance of setting boundaries between your workspace scopes. Let's try and **import a `store` lib** from an `api` scope. - In `apps/api/src/main.ts` - Try to `import { formatRating } from '@bg-hoard/store-util-formatters';`
    <br />

8.  **Run linting** on all projects - you should see another expected error.
    <br />

9.  You can now **delete the import** above.
    <br />

10. **Run linting** again and check if all the errors went away.

        💡&nbsp;&nbsp;Pass the suggested `--only-failed` option, so it doesn't relint everything.

    <br />

11. **Commit everything** before moving on to the next lab
    <br />

---

🎓&nbsp;&nbsp;If you get stuck, check out [the solution](SOLUTION.md)

---

[➡️ &nbsp;Next lab ➡️](../lab13/LAB.md)
