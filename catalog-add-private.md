---

copyright:
  years: 2019, 2021
lastupdated: "2021-04-01"

keywords: catalog, catalogs, private catalogs, account catalogs, catalog visibility, software visibility, import software

subcollection: account

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:external: target="_blank" .external}
{:beta: .beta}

# Adding products to a private catalog
{: #create-private-catalog}

You can use private catalogs to onboard your own products and centrally manage access to them for all users in your account. 
{: shortdesc} 

{: #sw-software-type}

Review the list of supported software:

  * Helm charts
  * Terraform templates
  * OVA images deployed on VMware vCenter Server
  * Virtual server images with Terraform deployed on VPC infrastructure
  * Operators deployed on Red Hat OpenShift

## Before you begin
{: #prereq-create}

* You need a [Kubernetes cluster](https://cloud.ibm.com/kubernetes/landing){: external} to validate your software.
* Make sure you're assigned the following roles:
  * Editor role on the catalog management service
  * Writer role on the {{site.data.keyword.secrets-manager_short}} service
  * Viewer role on all resource groups. Select **No service access**, a resource group name, then the role, and repeat for each resource group.
 if you're using a secret as part of adding your product from a private repository, 
  
  For more information, see [Assigning users access](/docs/account?topic=account-catalog-access).

* If you're using a secret to add products from a private repository:
  * [Writer service role for Secrets Manager](/docs/secrets-manager?topic=secrets-manager-iam)
  * Additional access for [integrating with other services](/docs/secrets-manager?topic=secrets-manager-integrations).
  
  {{site.data.keyword.secrets-manager_full_notm}} is a beta service that is available for evaluation and testing purposes only. Beta services might be unstable or change frequently.
  {: beta}

## Creating your private catalog
{: #create-catalog}

1. Go to **Manage** > **Catalogs**, and select **Private catalogs**. 
2. Click **Create**.
3. Select **Product default** as the catalog type. 
4. Enter a name and description of your catalog.
5. Select whether to exclude all products in the {{site.data.keyword.cloud}} catalog from your catalog.
6. Click **Create**.

## Adding a product to your catalog
{: #add-public-repo}

Complete the following steps to add a product to your catalog:

1. Click **Private catalogs**, and select your private catalog from the list.
1. Click **Private products** > **Add**.
1. Select whether you are adding your product from a private or public repository. 
1. Enter your repository's URL or TGZ archive. 
1. If you're adding your product from a private repository, you can choose to provide a personal access token or you can use a secret. Instead of giving users a personal access token, you can give them access to a secret and add the token to a secret and centrally manage all tokens and access the secret allows.

  * If you're using a personal access token, select **No** to indicate you aren't using a secret and provide your personal access token.
  * If you're using a secret, select **Yes** and click **Select from Secrets Manager**. Select your service instance, secret group, and secret. If you don't see your secret, make sure you're using the correct secret group and service instance. 
    
    The message `No service instance available` might be displayed if you haven't created a secret or if you don't have the correct access to use secrets, even if you have service instances that are created. 
    {: note}

1. Select a category and your deployment target.
1. Click **Add**. 

## Publishing your product 
{: #validating-software}

After you add your product to your catalog, you're ready to publish it to your account. Complete the following steps to validate and publish your product:

1. Review the product details by selecting **Configure product**, **Add license**, and **Edit readme**. 
1. Select **Validate product**. 
1. Select a cluster. 
1. Click **Validate** to ensure that your product can be installed successfully. This step is required before you can make it available for users in your account, and the validation can take several minutes. In the Validation summary section, the status is displayed as Validated when the deployment is complete. 
1. Click the **Actions** icon ![Actions icon](../icons/actions-icon-vertical.svg), and select **Publish to account** to make your product available to all users in your account through your private catalog.


## Adding your product by using the CLI
{: #create-cicd-product}

Complete the following steps to add your software by using the CLI. You can use this task in a CI/CD process.
    
1. Add software to your private catalog. For more information, see the [cli documentation](/docs/cli?topic=cli-manage-catalogs-plugin#create-offering) for adding software to your private catalog.  
    ```
    ibmcloud catalog offering create --catalog "Name of catalog" --zipurl https://software.url.com.tgz
    ```
    {: codeblock}
    
1. Add the **Developer Tools** category. For more information, see the [cli documentation](/docs/cli?topic=cli-manage-catalogs-plugin#add-category-offering) for adding a category.  
    ```
    ibmcloud catalog offering add-category --catalog "Name of catalog" --offering "software-offering" --category "Developer Tools"
    ```
    {: codeblock}
    
1. Validate the software. For more information, see the [cli documentation](/docs/cli?topic=cli-manage-catalogs-plugin#validate-offering) for validating the software. 
    
    You need the version locator for your software. To find it, run the **`ibmcloud catalog offering list --catalog "Name of catalog"`** command, and search for the particular version of your software. Also, use the cluster that you created when you set up the required resources. 
    {: important}
    
    ```
    ibmcloud catalog offering validate --version-locator <LOCATOR> --cluster <CLUSTER> --namespace "software-offering"
    ```
    {: codeblock}
    
    Deploying the software can take a few minutes. You can check the validation status by querying the product validation state. The validation is complete when the state is Valid. For more information, see the [cli documentation](/docs/cli?topic=cli-manage-catalogs-plugin#validate-status-offering) for validation status.
    ```
    ibmcloud catalog offering validate-status --version-locator <LOCATOR>
    ```
    {: codeblock}
    
1. Publish your software to make it available to users in your account. For more information, see the [cli documentation](/docs/cli?topic=cli-manage-catalogs-plugin#publish-offering-to-account) for publishing to your account.
    ```
    ibmcloud catalog offering publish-to-account --version-locator <LOCATOR>
    ```
    {: codeblock}

