# Paprika Outlook

> Send Anywhere Add-in for Office 365 Outlook

## Prerequisites

- Node version: 18.18.2, if not installed, run nvm install 18.18.2
- run `npm install` after cloning this repository

## Run in local development environment

1. Run [**PAPRIKA WEB**](https://github.com/Rakuten-MTSD-PAIS/paprika_web) dev server locally.
    Run `npm run watch` or `npm run watch:local` (from root directory of paprika web)

    For details, please refer to [PAPRIKA WEB](https://github.com/Rakuten-MTSD-PAIS/paprika_web)

2. Run `npm run dev-server` from root directory of [**PAPRIKA_OUTLOOK**](https://bitbucket.org/estmob/paprika_outlook/src/develop/), which is **THIS** project you're working on.

3. Follow one of the instructions that suits your desired task.

- **To test React.js app in web browser**

    - Visit "https://localhost:3000/outlook.html"
    - **WARNING** : please make sure that you're using **HTTPS** protocol, not HTTP. Otherwise, you will not be able to test the add-in.

- **To test in your Outlook Desktop Application**

    1. Open your Outlook Desktop Application
    2. Click `···` button on the top menu bar
    3. Click `Get Add-ins` button
    4. Click `My Add-ins` button on the left side menu
    5. Scroll down to click `+ Add to Outlook` button
        - Click `Add from File...` button
    6. Upload `./manifest.xml`, which is located at the root directory
    7. If successfully added, you can see `localhost Send Anywhere` button on clicking `···` button on the top menu bar when writing an email

For details, please refer to : [Sideload Office Add-ins on Mac](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/sideload-an-office-add-in-on-mac)

- **To test in your Outlook Web Application**

    - Please refer to : 
    [Sideload Office Add-ins in Office on the web for testing](https://docs.microsoft.com/en-us/office/dev/add-ins/testing/sideload-office-add-ins-for-testing)


## Run in test, staging environment
- Follow one of the instructions that suits your desired task.

    - **To test React.js app in web browser**
        - Visit https://test.send-anywhere.com/web/outlook/ for test environment
        - Visit https://web-staging.send-anywhere.com/web/outlook/ for staging environment

- **To test in your Outlook Desktop Application**

    1. Folow steps 1~5 of 'To test in your Outlook Desktop Application' under step 3 of [Run in local development environment](#run-in-local-development-environment)
    2. **Upload `./manifest/manifest-staging.xml` for staging environment, `./manifest/manifest-test.xml` for test environment.**
    3. If successfully added, you can see `Test(or Staging) Send Anywhere` button on clicking `···` button on the top menu bar when writing an email
    
    For details, please refer to [Sideload Office Add-ins on Mac](https://learn.microsoft.com/en-us/office/dev/add-ins/testing/sideload-an-office-add-in-on-mac)

- **To test in your Outlook Web Application**

    - Please refer to 
    [Sideload Office Add-ins in Office on the web for testing](https://docs.microsoft.com/en-us/office/dev/add-ins/testing/sideload-office-add-ins-for-testing)

## Build

- To be updated

## Deployment

- To be updated

## Notes

- Every feature of send-anywhere Outlook add-in works properly inside Outlook web/desktop application.

    - If only the react app is tested in web browser, it will not work properly.

    - For example, if you visit https://send-anywhere.com/web/outlook/ in web browser, social login will not work.

- Adding add-ins to Outlook web application seems to be not supported as of now.

## TODO

- generate scripts that can automatically upload manifest file to Outlook desktop application, or just simply open Outlook desktop app.
