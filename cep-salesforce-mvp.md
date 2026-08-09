---
status: published
product: cep
last-updated: 2026-08-08
---

# How to Restrict Salesforce Access to Managed Chrome Browsers Only (MVP)

## Overview
This Minimum Viable Product (MVP) guide demonstrates how to use Chrome Enterprise Premium to lock down access to Salesforce. Once configured, users can only authenticate into Salesforce if they are using a corporate-managed Chrome browser. Any attempts to log in using unmanaged Chrome profiles, Firefox, Safari, or Edge will be explicitly blocked at the identity provider level.

## Prerequisites
- A Google Workspace account with Chrome Enterprise Premium licensing.
- Super Administrator privileges.
- Salesforce configured to use Google Workspace for Single Sign-On (SAML).
- **Endpoint Verification** extension force-installed to your users' managed Chrome browser profiles via Google Admin console.

## Steps

**1. Create the "Managed Chrome Only" Access Level**
1. Navigate to the **Google Cloud Console** > **Security** > **Access Context Manager**.
2. Ensure you are at the Organization level in the top project drop-down.
3. Click **Create Access Level**.
4. Name the access level: `Require_Managed_Chrome`.
5. Under the **Conditions** section, click on **Device Policy**.
6. Check the box for **Require Endpoint Verification**. 
   *(Note: This ensures the browser is actively reporting its state to Google, which only happens on managed Chrome profiles where you have pushed the extension).*
7. *(Optional but recommended)* Check **Require Corporate Owned Device** if you also want to block personal computers running managed Chrome.
8. Click **Save**.

**2. Assign the Access Level to Salesforce**
1. Navigate to the **Google Admin console** (`admin.google.com`).
2. Go to **Security** > **Access and data control** > **Context-Aware Access**.
3. Click on **Assign Access Levels**.
4. Search for and select your **Salesforce** SAML application from the list of Apps.
5. Click **Assign** and check the box next to the `Require_Managed_Chrome` Access Level you just created.
6. Click **Save** to enforce the policy.

## Verification
1. **The Negative Test (Should Fail):** Open Firefox, Safari, or a personal/Guest Chrome profile. Navigate to your Salesforce login page and attempt to sign in with your Google Workspace credentials. You should be intercepted by a Google error screen stating: *"Your organization's policy blocks access to this app."*
2. **The Positive Test (Should Succeed):** Open your managed Chrome browser (where the company profile is logged in and the Endpoint Verification extension is running). Navigate to Salesforce and log in. You should be granted access seamlessly.

## Troubleshooting
- **Users on managed Chrome are getting blocked:** Have the user click the Endpoint Verification puzzle piece extension icon in their browser and click "Sync Now". The browser may not have reported its managed state to Google yet.
- **Access Level doesn't show up in Admin Console:** Ensure you created the Access Level at the *Organization* level in Google Cloud, not inside an isolated GCP project. 
- **Users bypass the block:** Ensure Salesforce is strictly enforcing Google SSO and users cannot bypass it by typing a local Salesforce username and password.

## Next Steps
- Now that the browser is locked down, configure **Chrome Enterprise Premium Data Loss Prevention (DLP)** rules to prevent users from copying/pasting or downloading sensitive customer data out of Salesforce.
