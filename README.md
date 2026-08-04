name: Update Profile Stats

on:
  schedule:
    - cron: "0 3 * * *"   # runs daily at 03:00 UTC
  workflow_dispatch:        # lets you trigger it manually from the Actions tab

permissions:
  contents: write

jobs:
  update-stats:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repo
        uses: actions/checkout@v6

      - name: Generate stats card
        uses: stats-organization/github-readme-stats-action@v2
        with:
          card: stats
          options: username=${{ github.repository_owner }}&show_icons=true&count_private=true&hide_border=true&title_color=00D9FF&icon_color=00D9FF&text_color=c9d1d9&bg_color=0d1117
          path: profile/stats.svg
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Generate top languages card
        uses: stats-organization/github-readme-stats-action@v2
        with:
          card: top-langs
          options: username=${{ github.repository_owner }}&layout=compact&langs_count=8&hide_border=true&title_color=00D9FF&text_color=c9d1d9&bg_color=0d1117
          path: profile/top-langs.svg
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Generate streak stats
        uses: be-next/github-streak-stats-action@v1
        with:
          username: ${{ github.repository_owner }}
          token: ${{ secrets.GITHUB_TOKEN }}
          output-path: profile/streak-stats.svg
          theme: dark
          hide-border: true

      - name: Commit and push changes
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add profile/*.svg
          git diff --staged --quiet || git commit -m "Update profile stats cards"
          git push
