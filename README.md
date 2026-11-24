# ui-lab

Automated UI testing lab (ITCS386, W12). Tool parts chosen: **Part 3, Robot Framework** and **Part 4, Cypress**. Both automate test case QP-01 (a custom pizza reaches the admin list) on https://quickpizza.grafana.com. The answers, versions and run results are in RESULTS.md.

## robot/

Robot Framework 7.5.1 with SeleniumLibrary 6.9.0: the first tests, the keyword file robot/resources/quickpizza.resource, the scenario pizza_scenario.robot, and the evidence of two full runs in robot/results (log.html, report.html, output.xml, screenshots).

Run: set CHROMEDRIVER to the path of your ChromeDriver, then

    cd robot && pip install -r requirements.txt && robot -v BROWSER:headlesschrome pizza_scenario.robot

## cypress/

Cypress 16 specs for the web form and for QP-01 (cypress/cypress/e2e), the custom command adminLogin (cypress/cypress/support/commands.js), and the evidence of two full runs: screenshots and mp4 videos in cypress/cypress/screenshots and cypress/cypress/videos.

Run:

    cd cypress && npm install && npx cypress run --browser chrome

## GitHub Actions

The runs of this repository were made on a GitHub Actions runner with headless Chrome, started by hand under Actions: workflow robot (.github/workflows/robot.yml) and workflow cypress (.github/workflows/cypress.yml). Each workflow commits its own evidence back to main.

## Note on tools

The code in this repository was written with the help of an AI assistant (Claude) following the lab sheet, and was run on the runner described above.
