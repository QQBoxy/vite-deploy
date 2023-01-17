# Vite Deploy

## Make DeployToken

1. Your personal account settings > Developer settings > Personal access tokens (classic) > Generate new token (classic)
2. Type Note and tick `repo` .
3. Copy `token` .
4. Go to repo Settings > Secrets and variables > Actions secrets > New repository secret
5. Type name as `DeployToken` and paste `token` in Secret.

## Deploy Settings

1. Add yaml file.

**.github\workflows\github-actions.yml**

```yaml
name: Deploy

on:
  push:
    branches:
      - main

jobs:
  build:
    name: Build
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repo
        uses: actions/checkout@v2

      - name: Setup Node
        uses: actions/setup-node@v1
        with:
          node-version: 16

      - name: Install dependencies
        uses: bahmutov/npm-install@v1

      - name: Build project
        run: npm run build

      - name: Upload production-ready build files
        uses: actions/upload-artifact@v2
        with:
          name: production-files
          path: ./dist

  deploy:
    name: Deploy
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'

    steps:
      - name: Download artifact
        uses: actions/download-artifact@v2
        with:
          name: production-files
          path: ./dist

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.DeployToken }}
          publish_dir: ./dist
```

## Congratulations