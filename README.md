name: Run start.py every 5 minutes on weekdays

on:
  schedule:
    - cron: "*/5 9-18 * * 1-5"
  workflow_dispatch:

concurrency:
  group: scheduled-task
  cancel-in-progress: false

jobs:
  run-script:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
        with:
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: |
          pip install -r requirements.txt

      - name: Run script
        run: python start.py
        env:
          SESSION: ${{ secrets.SESSION }}
          AI_KEY: ${{ secrets.AI_KEY }}
          ENNCY_KEY: ${{ secrets.ENNCY_KEY }}
          FILTERED_COURSES: ${{ secrets.FILTERED_COURSES }}

      - name: Commit and push changes
        run: |
          git config --global user.name "github-actions[bot]"
          git config --global user.email "github-actions[bot]@users.noreply.github.com"
          git add .
          git commit -m "Auto update logs" || echo "No changes to commit"
          git push origin main
