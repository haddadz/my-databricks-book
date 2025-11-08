# my-databricks-book
my-databricks-book


Publishing a Databricks notebook to GitHub Pages using Jupyter Book with CI/CD, via GitHub Actions
Below complete, production-ready solution:

GitHub Actions (Recommended – Free & Native)
Goal:
Every time you push an updated .ipynb (exported from Databricks) to the main branch → GitHub Actions:

Builds the Jupyter Book
Deploys it to GitHub Pages automatically

Step-by-Step Setup
1. Prepare Your Repository
Ensure your repo has:
textmy-databricks-book/
├── _config.yml
├── _toc.yml
├── content/
│   └── your-notebook.ipynb   # Exported from Databricks
├── .github/
│   └── workflows/
│       └── deploy-book.yml
2. Create the GitHub Actions Workflow
Create the file:
.github/workflows/deploy-book.yml
yamlname: Deploy Jupyter Book to GitHub Pages

on:
  push:
    branches: [ main ]
  workflow_dispatch:  # Allows manual trigger

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pages: write
      id-token: write

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'
          cache: 'pip'

      - name: Install dependencies
        run: |
          pip install jupyter-book ghp-import

      - name: Build Jupyter Book
        run: |
          jupyter-book build .

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./_build/html
          force_orphan: true

Why peaceiris/actions-gh-pages?
It’s more reliable than ghp-import in CI and supports GitHub's new GitHub Pages + Actions deployment method.


3. Enable GitHub Pages

Go to Settings > Pages in your repo.
Under Source, select:
Deploy from a branch
Branch: gh-pages
Folder: / (root)

Save.


After first deployment, your site will be at:
https://<username>.github.io/<repo-name>/


4. Automate Notebook Export from Databricks (Optional but Powerful)
Use Databricks Repos + GitHub Sync to auto-export .ipynb on save.
Enable Databricks Repos:

In Databricks → User Settings > Git Integration
Link to your GitHub repo
Set Git sync mode to Pull and push
Choose Export format: Jupyter (.ipynb)

Now:

Every time you edit and save a notebook in Databricks → it pushes .ipynb to GitHub → triggers GitHub Actions → deploys updated book!