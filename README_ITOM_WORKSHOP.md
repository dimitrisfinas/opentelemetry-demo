

# CONFIGURE SGC FOR LIGHTSTEP

- In `Favorites` menu, look for `SG Connector for Observability Lightstep` and click `Setup` option

- Follow the step by step setup instructions using values below:
  1. For step "Configure the Connection"
    * Set your Organization and Project
        - organization: XXX
        - project: XXX
    *  For task "Set your API Key"
        - api key: XXX
    * Configure OpenTelemetry
        - Protocol: http
        - Host: 34.105.68.111    
    * Test the Connection
        - click "configure" button and wait for test to finish
        - Check that State is marked as `Complete`
        - Check that Completion code is `Success`

  2. For step "Set up Scheduled Import Jobs"
    * Configure the Scheduled Import
        - Open the job named `SG-OpenTelemetry Resources Scheduled Import`
        - Click the `Execute Now` button and wait few seconds
        - You can see the result by opening a new window on `Favorites` menu, look for `SG Connector for Observability Lightstep` and click `Lightstep Services` option
        - A second job creating relationships will run automatically after this one, you can check the result by opening `Favorites` / `Dependency Views`/ `View Map` and searching for any CI beginning with `LS`
        - you don't need to run the other jobs for this workshop

  3. For step "Event Management"
    * Review Push Connector
        - Directly mark this task as Complete
    * Create Lightstep Webhook Destination
        - Before starting this task, connect to your lightstep instance and go to alert destinations
        - Check that you have not created any destination yet
        - Click the `Configure` button
        - Click `Create Webhook` button
        - Check that the webhook is created in Lightstep by refreshing your screen
        - Edit the webhook just created to add header information `service-name` with value `frontend` (this is temporary limitation with this version of SGC as future version will do direct mapping to major service used in Alert)

    * Test the Event
        - you can test that the event is working well, by clicking `Test` button in Lightstep and looking at "All events" in ServiceNow from the one coming from source "*lightstep"




## TROUBLESHOOT
- State marked as `Cancelled` when testing the connection
    - Check that your organization and project names are correct
    - Be careful, they are case sensitive!

- Event Management Webhook not created in Lightstep
    - Go to Flow Designer, and search for last executions of flow named `... Lightstep Create Webhook`, look for possible errors in this flow (even if marked as complete and global state as Success)
    - if 403 of `forbidden` error, check if yoru organization and project names are correct (Be careful, they are case sensitive!), if ok change your Lightstep API key (be sure to take an api key with member role)
