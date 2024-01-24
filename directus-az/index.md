---
title: 'Title: How to Deploy Directus on Azure Web service'
description: 'This is an how to tutorial on how to self host directus using Azure Web App service.'
author:
  name: 'Durojaye Olusegun'
  avatar_file_name: 'segun_avater.jpeg'
---

## Introduction

This guide outlines the steps to deploy Directus on Azure using Docker, with a focus on utilizing PostgreSQL as the database, providing a scalable and efficient solution for your content management needs.

## Before You Start

Before deploying Directus on Azure, make sure you have the following prerequisites:

- **Azure Account:** Obtain an active Azure account or [sign up](https://go.microsoft.com) for a free one on the Azure portal. Also, understand basic navigation in the Azure Portal for efficient resource management.

- **Docker:** A basic understanding of Docker is required.

## Create and Set Up a Resource Group

To begin the deployment process, you need to set up a resource group in Azure. A resource group acts as a logical container to group and manage related Azure resources. It allows you to organize your resources, control access permissions, and manage them collectively.

Sign into your Azure account via the [Azure Portal](https://portal.azure.com/). Head to Resource groups and click the **Create** button.

Provide a unique name for your resource group and choose the Azure subscription to link your new group to. Select the appropriate region for your resource group, considering factors like data residency and proximity to users, and adjust other settings if required.

## Setting Up Azure Database for PostgresSQL

Directus connects to an existing database, so it's time to create one.

Enter your new resource group and, in the Overview pane, click the **Create** button to initiate the resource creation process.

In the Azure Marketplace pane, search for and select a Azure Database for PostgreSQL resource.

Secure your new database with PostgreSQL authentication, a strong password, and consider firewall rules for additional protection.

![Credentials for Azure PostgreSQL.](credentials-postgres.png)

Save the server's name, username, and password for later use when configuring Directus.

Finally, click on **Review + Create** and then **Create** to create your new PostgreSQL Database deployment.

## Preparing a Docker Configuration File

Create a `docker-compose.yml` file on your local computer and open it in your text editor. Copy and paste the following before saving:

```yml
version: "3"
services:
  directus:
    image: directus/directus:10.8.3
    ports:
      - 8055:8055
    volumes:
      - ${WEBAPP_STORAGE_HOME}/database:/directus/database
      - ${WEBAPP_STORAGE_HOME}/uploads:/directus/uploads
    environment:
      KEY: "replace-with-random-value"
      SECRET: "replace-with-random-value"
      ADMIN_EMAIL: "admin@example.com"
      ADMIN_PASSWORD: "d1r3ctu5"
      DB_CLIENT: "pg"
      DB_HOST: "YOUR_PDS_DB_URL"
      DB_PORT: 5432
      DB_DATABASE: "postgres"
      DB_USER: "YOUR_DB_USER"
      DB_PASSWORD: "YOUR_DB_PASSWORD"
      WEBSOCKETS_ENABLED: true
```

Let’s go through some of the key parameters in this configuration file above:

Set the `DB_HOST` value to the your Azure Database for PostgreSQL's server name. You can find it in the resource's overview section.

Also set `DB_USER and DB_PASSWORD` to the credentials you set up during the creation of your Azure Database for PostgreSQL.

`${WEBAPP_STORAGE_HOME}` is automatically populated by the Azure App Service that is mapped to persistent storage for your Directus project.

## Deploying Directus on a Web App Service

Within the Azure Marketplace, select the Web App resource.

When creating a Web App, you will step through multiple configuration pages. Here are the important settings:

### Basics

- Subscription/Resource Group: select the same resource group we created and used earlier.
- Publish: Select "Docker Container".
![Azure Web App Basic Configuration](web-configuration.png)

### Docker

Select Docker Compose and Docker Hub as the source for your app's configuration. Set the Docker Hub Access Type to Public and upload your `docker-compose.yml` file prepared earlier.

![Docker congiguration settings](docker-configuration.png)

Following the creation of the Web App Resource, Directus is now successfully deployed and can be visited via the default domain in the Azure Web App page.

## Troubleshooting Tips

Here are few troubleshooting tips:

1. **Connection Issues with Azure Database for PostgreSQL**

If you encounter connectivity problems between Directus and your Azure Database for PostgreSQL, consider the following steps:

- **Firewall Rules:** Ensure that the firewall rules for your Azure Database allow connections from the Azure Web App. You can configure this in the Azure Portal under the *Connection Security* section for your PostgreSQL server.

- **Connection String:** Double-check the values in your docker-compose.yml file for `DB_HOST`, `DB_USER`, `DB_PASSWORD`, and other related parameters. Any discrepancies here can result in connection failures.

2. **Azure Web App Deployment Failures**

In case your Azure Web App deployment fails, consider the following:

- **Docker Image Compatibility:** Ensure that the Directus Docker image version specified in your `docker-compose.yml` file is compatible with the Azure Web App environment. Check for updates or use a different version if needed.

- **Resource Group Permissions:** Confirm that the Azure account used for deployment has the necessary permissions to create and manage resources within the specified resource group.

- **Docker Configuration Validation:** Validate your `docker-compose.yml` file for syntax errors or inconsistencies. Incorrect configurations can lead to deployment failures.
  
3. **Directus Interface Login Issues**

If you experience problems logging into the Directus interface:

- **Admin Credentials:** Ensure that the `ADMIN_EMAIL` and `ADMIN_PASSWORD` values in your `docker-compose.yml` file match the credentials you are using to log in.

- **Environment Variable Changes:** If you make changes to environment variables after the initial deployment, restart the Directus container to apply the new configurations.

## Summary

This tutorial has guided you through setting up a resource group, configuring Azure Database for PostgreSQL, and deploying Directus using Docker on an Azure Web App.