# KDB config
Edit this file to update KDB's settings. No technical knowledge needed —
just replace the placeholder values with your own URLs and names.

## Notion URLs
comms_guidelines: YOUR_NOTION_COMMS_GUIDELINES_URL
slack_update_template: YOUR_NOTION_SLACK_UPDATE_TEMPLATE_URL
product_specs_database: YOUR_NOTION_PRODUCT_SPECS_DATABASE_URL
launch_tracker: YOUR_NOTION_LAUNCH_TRACKER_URL
roadmap: YOUR_NOTION_ROADMAP_URL
meetings_db: YOUR_NOTION_MEETINGS_DATABASE_URL

## Slack channels
product_updates: #YOUR_PRODUCT_UPDATE_CHANNEL
daily_standup: #YOUR_STANDUP_CHANNEL
announcements: #YOUR_ANNOUNCEMENTS_CHANNEL
weekly_brief: #YOUR_BRIEF_POSTING_CHANNEL

## Amplitude dashboards
north_star: YOUR_AMPLITUDE_DASHBOARD_URL
growth: YOUR_AMPLITUDE_DASHBOARD_URL

## Looker dashboards
# Add your key dashboard IDs here. KDB queries these directly.
# Format: name: https://yourinstance.looker.com/dashboards/NNN
main: YOUR_LOOKER_DASHBOARD_URL

## Weekly brief
channel: #YOUR_BRIEF_POSTING_CHANNEL
schedule: 9am Monday

dashboards:
  - name: YOUR_DASHBOARD_NAME
    source: Looker
    id: YOUR_DASHBOARD_ID
    metrics: YOUR_METRIC_1, YOUR_METRIC_2
  - name: YOUR_DASHBOARD_NAME
    source: Amplitude
    id: YOUR_AMPLITUDE_DASHBOARD_ID
    metrics: YOUR_METRIC_1, YOUR_METRIC_2

wow_metrics: YOUR_METRIC_1, YOUR_METRIC_2, YOUR_METRIC_3

## Competitor Analysis
# Competitor pricing scripts are in scripts/competitor-pricing/
# Before first use:
#   cd scripts/competitor-pricing && npm install
#   node_modules/.bin/playwright install chromium
#
# Config files:
#   scripts/competitor-pricing/competitors.json — competitor list
#   scripts/competitor-pricing/competitors-weight-loss.json — category focus
#
# Output: reports/competitor/runs/YYYY-MM-DD/ and reports/competitor/

## People
# Stakeholder intelligence — used by /stakeholder-scan and /prep.
# Slack IDs: from Slack profile > copy member ID (format: U0123456789).
# Notion IDs: from Notion profile URL.

peers:
  - name: "TEAM_MEMBER_NAME"
    slack_id: YOUR_SLACK_ID
    notion_id: YOUR_NOTION_ID
    role: "YOUR_ROLE"
    group: team
  - name: "TEAM_MEMBER_NAME"
    slack_id: YOUR_SLACK_ID
    role: "YOUR_ROLE"
    group: team

leadership:
  - name: "LEADER_NAME"
    slack_id: YOUR_SLACK_ID
    notion_id: YOUR_NOTION_ID
    role: "YOUR_ROLE"
    group: leadership
    slug: your-slug

cross_team:
  - name: "CROSS_TEAM_NAME"
    slack_id: YOUR_SLACK_ID
    role: "YOUR_ROLE"
    group: cross-team
    slug: your-slug

monitored_channels_for_people:
  - "#YOUR_MAIN_TEAM_CHANNEL"
  - "#YOUR_PRODUCT_CHANNEL"
  - "#YOUR_ANNOUNCEMENTS_CHANNEL"

monitored_notion_pages_for_people:
  - YOUR_NOTION_MEETINGS_DATABASE_URL

## Jira
default_project_key: YOUR_PROJECT_KEY
jira_board: YOUR_JIRA_BOARD_URL
