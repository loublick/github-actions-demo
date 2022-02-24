# Azure Web App and GitHub Actions demo and quick start template

This repository is meant to help bootstrap users of the Azure Web App service to get started in building and using GitHub Actions to build applications and deploy application.

## What's In This Repo?

### This repo contains a web app and instructions on how to provision the Azure services and build and deploy the web app.
---
Languages:
- csharp
- aspx-csharp
<<<<<<< HEAD
page_type: sample
description: "This is a sample application you can use to follow along w/ the Build a .NET Core and SQL Database web app in Azure Web Apps for Containers tutorial."
products:
- aspnet-core-5
=======

Products:
>>>>>>> a80045e443a69ddd2cbe98c55b0c51926d1d3f8e
- azure
- azure-app-service
- azure-sql
---

## License

See [LICENSE](LICENSE.md).

## Contributing

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Instructions

* [Clone git repo and select the branch]
    * git clone https://github.com/loublick/github-actions-demo
    * git branch -m main

* [Run the local SQLite database migrations and run the app locally to confirm the build]
  * dotnet ef database update
  * dotnet run
  * http://localhost:5000

* [Create the Azure resource group]
  * az group create --name <rg_name> --location "<location>"

* [Create the Azure SQL Database server]
  * az sql server create --name <db_srv_name> --resource-group <rg_name> --location "<location>" --admin-user <db_admin_user_name> --admin-password <db_admin_user_pwd>

* [Create the Azure SQL database and display the connection string]
  * az sql db create --resource-group <rg_name> --server <db_server_name> --name <db_name> --service-objective <Srv_obj>
  * az sql db show-connection-string --client ado.net --server <db_server_name> --name <db_name>
  * Copy and save the connection string for use later

* [Update the application code to connect to the Azure SQL Database instead of the local SQLite database]
  * Update the Startup.cs file
    [Replace:] services.AddDbContext<MyDatabaseContext>(options => options.UseSqlite("Data Source=localdatabase.db"));
    [With:] services.AddDbContext<MyDatabaseContext>(options => options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));

* [Delete the database migrations associated with the SQLite database]
  * rm -r Migrations

* [Recreate database migrations to use Azure SQL database]
  * dotnet ef migrations add InitialCreate

* [Set environment variable to database connection string in Powershell.]
  * Use the connection string saved previously
    * $env:ConnectionStrings:MyDbConnection="<db_connect_string>"

* [Run database migrations for Azure SQL Database]
  * dotnet ef database update

* [Run the web app locally with the Azure SQL Database]
  * dotnet run

* [Prepare code changes for commit and perform the commit]
  * git add .
  * git commit -m "Connect to Azure SQL database"

* [Configure the web app deployment user]
  * az webapp deployment user set --user-name <app_user_name> --password <app_user_pwd>

* [Create the App Service Plan]
  * az appservice plan create --name <app_srv_plan_name> --resource-group <rg_name> --sku <sku_name>

* [Create the web app]
  * az webapp create --resource-group <rg_name> --plan <app_srv_plan_name> --name <web_app_name> --deployment-local-git
  * Save the local git repo URL from the command output for later use

* [Get local IP of Azure web app]
  * az webapp config hostname get-external-ip --webapp-name <web_app_name> --resource-group <rg_name>
  * Save the IP address for later use

* [Disable access to the Azure SQL database for all IP address ranges]
* az sql server firewall-rule create --resource-group <rg_name> --server <db_server_name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0

* [Enable access for the Web App client IP address range and your local workstation client IP address]
  * az sql server firewall-rule create --name AllowLocalClient --server <db_server_name> --resource-group <rg_name> --start-ip-address=<webapp-ip> --end-ip-address=<webapp-ip>

* [Configure the web app with the connection string for the Azure SQL Database]
  * az webapp config connection-string set --resource-group <rg_name> --name <web_app_name> --settings MyDbConnection='<db_connect_string>' --connection-string-type SQLAzure

* [Configure the Azure deployment to use the main branch of the remote git repo]
  * az webapp config appsettings set --name <web_app_name> --resource-group <rg_name> --settings DEPLOYMENT_BRANCH='<branch>'

* [Add a remote deployment git repo using the local git repo URL for the web app that was saved previously]
  * git remote add azure <web_app_local_repo_url>

* [Push to the Azure remote git repo to deploy the web app]
  * git push azure main

* [Run the web app deployed to Azure]
  * https://<web_app_name>.azurewebsites.net
