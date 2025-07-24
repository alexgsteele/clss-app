# Community Lifeline Status System (CLSS) Deployment

This repository contains the source code and automated deployment scripts to deploy the Community Lifeline Status System (CLSS) application.


## Project Structure

The key components of this repository are:

* **`.github/workflows/`**: This directory contains the GitHub Actions workflow files. Each file defines a deployment process for a specific hosting platform (e.g., `azure.yml` for Azure).
* **`clss_deployment.zip`**: A required zip file containing all the necessary files to run the deployment. This includes the core `deploy.js` script, the front-end application source code (in an `app` folder), and a configuration file (`config.json`).

## Core Deployment Script (`deploy.js`)

The central piece of automation is the `deploy.js` script, located within `clss_deployment.zip`. Regardless of the final hosting destination, this script connects to your ArcGIS organization and performs the following critical setup tasks:

* **Creates User Groups**: Sets up necessary user groups (e.g., `CLSS Admin`, `CLSS Assessor`).
* **Creates Feature Service**: Creates the core Feature Service that stores the application's data.
* **Creates Web Map**: Generates the Web Map used by the front-end application.
* **Registers OAuth Application**: Registers the application within ArcGIS to get a Client ID, enabling secure user authentication.
* **Sets Permissions**: Shares the created ArcGIS items with the appropriate user groups.

This script is designed to be called by any GitHub Actions workflow after the zip file is extracted.

## Configuration

The deployment process is configured entirely through GitHub Actions Secrets and Variables. You can set these up in your GitHub repository under **Settings > Secrets and variables > Actions**.

### Secrets (for sensitive data)

Create these as **Secrets**. They are encrypted and will not be exposed in logs.

#### Core ArcGIS Secrets (Required for all deployments)

| Secret Name     | Description                                                                                         |
| --------------- | --------------------------------------------------------------------------------------------------- |
| `ARCGIS_USERNAME` | The username of an ArcGIS Creator Account user with administrative privileges to create items and groups.   |
| `ARCGIS_PASSWORD` | The password of the ArcGIS Creator Account user.                                                    |

#### Deployment Target Secrets

##### Deployment to Azure Static Web App

If deploying to Azure Static Web app, configure the app as a Standard App in Azure prior to running the deployment. Once the pipeline runs, the application can be marked as the "Free" version in Azure. 

| Secret Name                                  | Description                                                                                        |
| -------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| `AZURE_STATIC_WEB_APP_DEPLOYMENT_TOKEN` | The deployment token for your specific Azure Static Web App. You can get this from the Azure portal under Manage Deployment Token |

*(Note: The `GITHUB_TOKEN` is a built-in secret provided by GitHub Actions and does not need to be created manually.)*

### Variables (for non-sensitive data)

Create these as **Variables** in an environment called **CLSS Prod**. They are stored as plain text and are safe to be viewed in logs. You can view the variables under **Settings > Secrets and variables > Actions** and create a new environment from the Variables tab. 

#### Core ArcGIS Variables (Required for all deployments)

| Variable Name     | Description                                                              |
| ----------------- | ------------------------------------------------------------------------ |
| `ARCGIS_PORTAL_URL` | The URL of your ArcGIS Online organization or ArcGIS Enterprise portal. Example for Enterprise: https://arcgis.gh-dev.com/portal, Example for ArcGIS Online:  https://ghis.maps.arcgis.com  |
| `CLSS_DOMAIN_URL` | The domain/url that the CLSS site will be accessed from. Example: https://www.houstonclss.com  |
| `ARCGIS_OBJECT_SUFFIX` | An optional set of characters to include in the deployed ARCGIS artifact names. For instance, if the suffix is entered as '_prod', the deployed feature service will be named CLSS_FeatureService_prod and the OAuth 2.0 app will be named CLSS_APP_prod. This is not a required field |


## Run Post Deployment Verification and Configuration

Open the 'CLSS Post Deployment Guide.pdf' file and run through the verification and configuration steps. 