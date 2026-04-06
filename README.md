# Salesforce-Automation-System
This Salesforce project is a hands-on build for managing service appointments and nudging people at the right time—by email or SMS—while keeping the messy stuff (retries, callouts, and batch jobs) organized and visible in a simple dashboard.

Trailhead Developer — Service ops sample
This repo is a Salesforce DX project I use as a portfolio-style build: service appointments, reminders, integrations, bulk processing, and a small dashboard—stuff that shows up in real enterprise work, not just a single trigger.

What’s in here (in plain English)
Reminders — Built around standard ServiceAppointment (Field Service). When an appointment looks “ready,” the automation creates ApptNotification__c rows from custom metadata rules (for example ~24h email, ~1h SMS—tune the rules to taste).
Async work — Batch + queueable so email and SMS callouts don’t fight governor limits.
Integration hub — Callouts can be logged with IntegrationCall__c, retry batches, and endpoint config in Custom Metadata.
Bulk / logging — JobConfig__mdt drives which batch classes run; ExecutionLog__c / ErrorLog__c capture runs.
UI — opsDashboard LWC is a simple ops view (counts, integration health, recent job runs).
Before you deploy
Field Service — Service appointments and related automation expect this.
On a scratch org, config/project-scratch-def.json includes Field Service flags—use that file when you create the org.
Remote sites — If SMS uses HTTP endpoints (e.g. httpbin.org for demos), add Remote Site Settings so callouts are allowed.
Deploy
Authorize your org (once):

sf org login web --alias myorg --set-default
Deploy metadata:

sf project deploy start --source-dir force-app --target-org myorg
Swap myorg for your alias (or omit --target-org if your default is already set).

After deploy (things I usually run once)
Schedule hourly reminders (optional):

AppointmentReminderScheduler.scheduleHourly();
Run the bulk job orchestrator (optional):

BulkJobRunner.scheduleHourly();
Add the dashboard — In Lightning App Builder, put the Operations Dashboard (c-ops-dashboard) on a Home or app page.

Notes that might save you a headache
ApptNotification__c — Some orgs already have a different Notification__c object, so this project uses ApptNotification__c for reminder rows. Integration calls still use a lookup named Related_Notification__c pointing at that object.
Trailhead boat modules — Boat-heavy LWCs and Apex that depended on Boat__c were removed from this tree so deploys to a clean org don’t fail. If you’re doing that superbadge, pull those pieces from Trailhead or Git history and restore the custom objects in the org.
Tests
Run Apex tests in the org (adjust alias and test level as needed):

sf apex run test --target-org myorg --result-format human --code-coverage --wait 10
Useful links
Salesforce DX Developer Guide
Salesforce CLI Command Reference
If something breaks on deploy, it’s usually Field Service off, a missing remote site, or a conflicting package in the org—happy to tighten the readme further once you know your target org.
