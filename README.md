# WordPress + Docker Compose + Azure + GitHub Actions
Running **WordPress** (WordPress site + MySQL database containers) in **Azure Container Instances**, using a **Docker Compose file** and a **GitHub Actions workflow**.

## Docker Compose File
  - The [docker-compose.yaml](https://github.com/evandonova/WordPress-Azure-GitHub-Actions/blob/main/docker-compose.yaml) file defines a **multi-container application** with two services: ```wordpress``` and ```db```.
  - Uses **official Docker images** from Docker Hub.
  - Maps **ports**.
  - Sets **environment variables** that containers need.
  - Has modified **volume** definitions to create storage accounts and volumes in Azure.

## GitHub Actions Workflow File
  - The [compose.yaml](https://github.com/evandonova/WordPress-Azure-GitHub-Actions/blob/main/.github/workflows/compose.yml) workflow file **deploys the WordPress + MySQL containers** defined in the Docker Compose file to Azure Container Instances.
  - It checkouts the repo -> logs in Azure through Azure CLI using credentials in GitHub secrets -> logs in Azure with Docker -> creates and uses ACI context -> runs the Docker Compose file.

## Steps to run:
  1) Create a **resource group** named "**WordPressRG**" in Azure.
  2) Run **commands** in Powershell using **Azure CLI** to get **resource group id**, also **create a service principal** and get the returned **JSON with Azure credentials**:

  ```
  **// Get the resource group id**
  $groupId = $(az group show `
  --name resource-group-name `
  --query id --output tsv)
  ```

  ```
  **// Create the service principal**
  az ad sp create-for-rbac `
  --scope $groupId `
  --role Contributor `
  --sdk-auth
  ```

  3) Create **GitHub secrets** for ```AZURE_CREDENTIALS```, ```AZURE_CLIENT_ID```, ```AZURE_CLIENT_SECRET``` and ```AZURE_TENANT_ID```.
  5) **Run the workflow file in GitHub Actions** and make sure the job doesn't hang and is successful:

<kbd><img src="https://github.com/evandonova/WordPress-Azure-GitHub-Actions/assets/69080997/d6847d29-af3f-4bf6-9b96-0a9bd8e9b77c" width="600"></kbd>

  6) **Azure resources** should be created:

<kbd><img src="https://github.com/evandonova/WordPress-Azure-GitHub-Actions/assets/69080997/74c9c104-de8b-4cb1-ad7a-f35064d19d0a" width="700"></kbd>

  7) After about 5 minutes, you should have **WordPress working** on the **provided port from Azure**:

<kbd><img src="https://github.com/evandonova/WordPress-Azure-GitHub-Actions/assets/69080997/3caeb42f-fcb7-415f-8c26-987fb98ab8da" width="330"></kbd>
<kbd><img src="https://github.com/evandonova/WordPress-Azure-GitHub-Actions/assets/69080997/7b755c3b-ae9f-4b7e-9877-e8ea6f1c68c5" width="600"></kbd>
