## Github Actions for Building &amp; Deploying React Applications

Building and deploying your React application to Github Pages is a bit more time consuming to setup, but it's not awful.

First, you are going to configure your project to enable automated building and deploying

## Install GitHub Pages Package

First, install the `gh-pages` package for your project, which simplifies the deployment of your React app to GitHub Pages:

```sh
npm install gh-pages --save-dev
```

## Configuring Deployment Scripts

In your `package.json` file, add the following jobs to the `scripts` section to automate the deployment process. You should already have the `dev`, `build`, and `preview` jobs defined.

```json
    "predeploy": "npm run build",
    "deploy": "gh-pages -d dist",
    "postdeploy": "echo 'Deployment complete!'",
```

Inside the `vite.config.js` file, add the `base` property seen deploy to the configuration to ensure that your React app is deployed correctly:

```js
export default defineConfig({
  base: "/<repository-name>/", // add repository name here
  plugins: [react()],
})
```

## Deploying to GitHub Pages

To deploy your React app to GitHub Pages, run the following command:

```sh
npm run deploy
```

Go to your Github repository and click the **Actions** tab to watch the automated task run and complete. Once it is done, your React client is now publicly available at:

```https://<your username>.github.io/<repository-name>/```

## Automating Deployment with GitHub Actions

This only allows you to manually trigger deploys from your local machine. That's _**not**_ what the goal is. You want a deployment to happen every time a branch is merged to `main`.

1. Switch to a new branch, or `develop`
2. Create a new directory named `.github/workflows` in the root of your repository.
3. Inside the `.github/workflows` directory, create a new file named `deploy.yml`.
4. Add the following workflow configuration to the `deploy.yml` file:
   ```yml
   name: "Deploy to GitHub Pages"

   on:
       push:
           branches:
               - master
               - main

   jobs:
       deploy:
           runs-on: ubuntu-latest

           steps:
               - uses: actions/checkout@v2

               - name: Setup Node.js
                 uses: actions/setup-node@v1
                 with:
                     node-version: "18"

               - name: Install dependencies
                 run: npm install

               - name: Build
                 run: npm run build

               - name: Deploy
                 uses: peaceiris/actions-gh-pages@v3
                 with:
                     github_token: ${{ secrets.GITHUB_TOKEN }}
                     publish_dir: ./dist
                     publish_branch: gh-pages
    ```
5. Commit and push the branch to your repository.

GitHub Actions will now automatically build and deploy your React app to GitHub Pages whenever you push changes to the main branch. You can monitor the workflow's progress directly within your GitHub repository under the **Actions** tab.

## (Optional) Read-write permissions

If you encounter any issues with the deployment process due to read-write permissions, you can update the permissions of the workflow secret by following these steps:

1. Navigate to your GitHub repository settings.
2. Under **Actions** -> **General** tab, enable the _Read and write_ permissions in the **Workflow permissions** section.
3. Click **Save changes** to update the permissions and re-run the workflow.