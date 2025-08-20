# ComparisonTrade through python
# VS code IDE




1. Introduction

Purpose: Define the objective of the automation strategy.

Scope:  Identify what will and will not be automated 

Stakeholders: QA leads, developers, product owners, etc. from different internal projects under DTP.

2. Test Automation Goals

Phase 1:

Universal automation  i.e. same framework can be leveraged by different projects under DTP e.g. Callypso, Cross Netting etc.

Automate regression test 

Reduce manual test effort

Loosely coupled automation pieces  

Enable user friendly automation ( amend/ run with ease)

Reporting mechanism

Ability to add test case by end user independently 

Phase 2:

Enable CI/CD- Jenkins, Ansible 

XML regeneration ways….? Trade cleanup?? Configurable XML??? Mask trade id post processessing e.g. Front office trade id: “<trade_id>_XX” in messageQueue table that way the link between tradeid and messageQueue table.  <addon feature/utility>

Reporting enhancements etc.

Performance testing/Non-functional testing 

3. Test Scope

DTP Back Office in scope areas are as follows: 

TPM ??

Operations

Settlements -

Payments

Accounting

Cash management

Reconciliation

Reporting

Trade Loader

GE – new fields

Control M Batch

Flows and Data

Data inputs to DTP

Data DTP outputs

In Scope: Above mentioned areas of testing will be our scope of automation.  <priorities to be discussed with dev>

Regression tests (one day/two days fixed number of trades in xml )

System tests /Functional tests

Front to Back tests/ n-n testing

Integration tests 

Data driven tests (xml of trades having static +dynamic data/additional in xml formats which are not in prod yet as an input)

Database validations (different tables validations before and after)

SFTP validations (file size, checksum/cmp, successful transfer over SFTP protocol)

EOD batch testing/Compare flat files pre and post (.dd/.txt)

Flexibility to execute any piece of code e.g. Configurable way to only execute eod batch automation 

UAT

SIT

Performance testing/Non-functional testing

Task station automation 

Out of Scope:

Upstream and Downstream application testing

Any DTP Product types not listed under the In Scope section of DTP test strategy in HSBC confluence. 

DTP UI visual validation (except for Task station)

Failures/report failures troubleshooting

One time validations

Security testing

4. Automation Test Tool Selection

Framework: TestNG / JUnit / Python/ Pytest/Shell scripts/Linux scripts/Whitelibrary/ etc.

CI/CD: Jenkins / Ansible/GitHub Actions / GitLab CI

Reporting:  HTML Reports ,allure report

Reason for tool selection: Open-source, team expertise and user friendly  tech stack

5. Test Environment

Define environments used for test automation: <links>

 ERG4(Prod) / ERG5(Release) (Linux): In DTP level we have number of environments available with the list of different Primary Trading System (PTS) and other interfacing systems required for testing.

DTP Test Environments & connectivity with interfacing systems is managed by an independent sub-team known as DTP Environment team.

Access to environments is controlled by DTP Environment team to ensure that testing is performed in managed environment to ensure integrity of the tests.

Sybase (database):<..>

IBM MQ access: <..>

SSH keys(jobparams) : SSH keys are mentioned in jobparams of EOD batch jobs.

Solace <…>

Topic<…>

6. Test Data Management

Use of mock data, synthetic data, or production-like data in xml file. 

Test data shall include all regions PO and all asset class to be verified.

Strategies for test data reusability (trade id generation/increment logic) and cleanup

Use data generation tools where necessary(shell script)

7. Automation Framework Design

Architecture Pattern: Data driven framework

Structure:

Test scripts: 

Scripts shall be loosely coupled, configurable,

Every stage shall put validations on database respective tables before and after processing e.g. (PO_CRE, PO_TRADES,PSEVENTS etc)

Retry/Polling time logic  -Messagequeue table has status (new,processed,failed) keep poll this table to check if all xmls are processed or not. <may be not required )

Every part/piece of the automation shall send report.

Each stage of automation shall proceed with report generation to next stage even after certain validations on event table or any other failures. 

Test xml : one day/two days of trade data (xml of trades having static +dynamic data)

Utilities : Reusable functions and libraries

Reports : Reports with passed and failed tests with proofs, publishing reports (via email/etc)

Reusable components:

SSH Authentication

XML test data file

DB access

MQ access

Test Modules and Owners

XML/XMLs feed to trade loader - Gulshan 

verify consumed by MQ , Solace, Topic

verify messagequeue table in db  

.

Verify database tables pre and post processing trades .Capture before /pre processing count verses after/post processing db count via query. - Gulshan

Verify psevents table, trade table, bo_transfer, bo_message (confirmation and payment messages post transfer), bo_cre (after transfer)for next stages: transfer ,confirmation, settlement, cash, accounting etc.  

 EOD batch , Configurable execution of EOD batches -Snehal

FX spot rates as input,  MTM files as an input to non-cash (pending termination trades in DTP are MTM)    -eod input (trade ids from front office -prod and create synthetic files)

have configurations for which batch to be ran (control+m executes the batch) . Just take configurations like  asset class, region and date (what batch to be ran from end user)

make use of existing control +m batch 

do one time configuration for SFTP i.e. sshkeys setups on ERG4 and ERG5 by referring jobparams. Setup both ERG4 and ERG5 environments SFTP ready by setting up ssh keys. 

Compare both original flat files (.csv/.dat/,txt/.dd) from ERG4 and ERG5 environments i.e. from original paths and not from path mentioned in SFTP module i.e. ../../EOD_SFTP and display the comparison report. -Snehal 

  SFTP automation-  (file size, checksum/cmp, successful transfer over SFTP protocol) files transfers between linux boxes ERG4 and ERG5 and vice versa -Snehal

Files transferred from ERG4 to ERG5 will be kept under some folder named e.g. /.../.../EOD_SFTP in both the environments in order to avoid duplicity in old path.

 Reporting-  a UI displaying console log/current status of jobs (filed, pending, in progress, passed etc) with logs to access by user- Gulshan

Reporting - a final report of test summary -Gulshan

all modules integration - Both GG and SW

CI/CD integration - Snehal

Email integration with reports as attachment/display in mail body etc. - Snehal

Shared folder of logs history/Jenkins reports  / Project wise folders :files/folder creation at shared folder and storing reports for each execution - Gulshan

8. CI/CD Integration

Tests will be triggered:

On pull requests

On nightly builds

On production deployments (sanity checks)

Integrate with CI pipeline

9. Reporting and Metrics

Test execution reports (daily/weekly)

Key metrics:

Test Pass Rate

Time to execute test suite

Defect leakage

10. Maintenance Plan

Regular review of automation suite

Refactor slow or flaky tests

Update tests for new features/releases

11. Risks and Mitigation Plan

Risk

Mitigation

Flaky Tests

Retry logic, isolate root causes

Tool Limitations

Evaluate alternate tools

Test Environment Instability

<Use containerized/stubbed environments>

Resource Availability

<Cross-train team members>

12. Team and Roles



Role

Responsibility

QA Lead/ Automation Engineer

Strategy owner, process improvement, Framework development, test scripting

QA Head, Product Owner, Client Manager

Review of the document 

Developer

Failures/report failures troubleshooting

Unit tests, testability improvements, Automation users, Automation review/feedback, Suggestion on additional features

DevOps

CI/CD setup, environment provisioning

13. Timeline and Milestones

Q<>/<date> Framework setup , automate different loosely coupled pieces  for phase 1

Q<>/<date>/End of September(Calypso/Ellipse) need to check with (Sophis/Cross Product Netting): Integrate all pieces of automation and test regression suite 

Q<>/<date> : CI Integration phase 2

Q<>/<date> Add performance and security tests phase 2

Q<>/<date> : Maintenance, optimization, and reporting enhancements phase2

14. Appendices

DTP diagram https://deltacapita.atlassian.net/wiki/x/BQCUMAE 

Coding standards and best practices

Sample test execution



<Design and timelines for modules to be added.>
