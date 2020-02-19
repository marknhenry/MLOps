# Setup Steps
## Step 1. The main Resource Group
Get `Owner` or `Contributor` access to a Resource Group from your __admin__. This is where you will create the workspace and other required resources.

or

Create a Resource Group on Azure (preferably with just letters and numbers)
## Step 2. Get and set the Repo
1. Fork this repo
2. Clone it to your machine
3. Navigate to `mlops\common\Variables.yml` and change the `RESOURCE_GROUP` to the resource group you created in step 1
## 3. Check services on the subscription
Check if ACI(Azure Container Instance) service is registered in your subscription: Try executing the command from the Cloud Shell in the portal. Instructions [here](https://docs.microsoft.com/en-us/azure/cloud-shell/quickstart).
    If you dont have access, ask your __admin__.

    `az provider show -n Microsoft.ContainerInstance -o table`

    if not registered, run the below command (you need to be the subscription owner in order to execute this command successfully)

    `az provider register -n Microsoft.ContainerInstance`
    
    If you dont have access, ask your __admin__.

## 4. Create an AD Service Account for an Application (Will be DevOps in a later Stage)
* On this link: https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal, follow the steps in the following sections: 
* Create an Azure Active Directory application
* Assign the application to a role
    * Note: MAKE SURE ITS AN OWNER, NOT A CONTRIBUTOR
* Get values for signing in
    * Note, grab the Application (client) ID, and the Directory (tenant) ID
* Create a new application secret
    * Note it down with the Application ID and Tenant ID in previous step.  You will need all 3 in 



## Step 5. DevOps Account
1. If you don't have Azure DevOps account, [create](https://dev.azure.com) one

2. Login to Azure Devops -> Enable preview feature called `Multi Stage Pipeline`. Instructions [here](https://docs.microsoft.com/en-us/azure/devops/project/navigation/preview-features?view=azure-devops).
3. Create a project from the devops portal (top right of the portal). If you have trouble then refer to [docs](https://docs.microsoft.com/en-us/azure/devops/organizations/projects/create-project?view=azure-devops)
4. Create Azure Resource Manager Service connection. This is needed for azure devops to connect to your subscription and create/manage resources.

    Go to `project settings` in bottom left of devops portal & select `Service Connections` and setup a Resource Manager connection. You have few options:
    * If you have `Contributor` or `Owner` access to the `Subscription` or a `Resource Group`
        * Select `Service Principal (Automatic)`
        * Select the scope of your choice (ideally select `Subscription` as scope and specific `Resource group`) 
        * Name of this Connection should be `AzureResourceManagerConnection`. Leave this checked `Allow all pipelines to use this connection`.

5. The following step is needed for additional security for the prediction service that we will deploy. Inorder to treat the service endpoint URI and API key as `secret` in the devops pipeline, create a variable group:
    1. In Azure Devops leftnav, navigate to `Pipeline` -> `Library`. Create a new `Variable group` by clicking `+ Variable`. Name it `MLOPSVG`
    2. Open the group and select `Allow access to all pipelines`
    3. Add two new variables `TMP_API_KEY` and `TMP_SCORING_URI`. For the values enter any value e.g. `dummy`. Click the `Lock` icon in the value to mark it `Secret`.
    4. Add the following variables:
        * RESOURCE_GROUP -> Resource 
        * SP_APP_ID -> Application (Client) ID
        * SP_APP_SECRET -> Secret
        * SUBSCRIPTION_ID -> Your subscription ID
        * TENANT_ID -> Directory (Tenant) ID

`Save` the changes to the Variable group


And you're done!