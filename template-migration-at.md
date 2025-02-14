---

copyright:
  years:  2024
lastupdated: "2024-10-10"

keywords:

subcollection: cloud-logs

---

{{site.data.keyword.attribute-definition-list}}


# Template for migrating Activity Tracker instances in the account
{: #template-migration-at}

Template to plan migration from {{site.data.keyword.at_full}} instances to {{site.data.keyword.logs_full_notm}} instances.
{: shortdesc}


## List of services that you might need for migration
{: #template-migration-at-1}

- [ ] List of services that you might need for migration:

    - [ ] Cloud Object Storage (to store data and metrics)

    - [ ] Event Notifications (to trigger alerts thorugh Email, PD, Slack, webhook)

    - [ ] {{site.data.keyword.logs_full_notm}} (the new logging service in IBM Cloud Observability)

## List of permissions that you might need for migration
{: #template-migration-at-2}

The following list outlines the IAM permissions that you need to migrate:

If you have the IAM permission to create policies and authorizations, you can grant only the level of access that you have as a user of the target service. For example, if you have viewer access for the target service, you can assign only the viewer role for the authorization. If you attempt to assign a higher permission such as administrator, it might appear that permission is granted, however, only the highest level permission you have for the target service, that is viewer, will be assigned.
{: important}

For more information on permissions, see [Required permissions](/docs/cloud-logs?topic=cloud-logs-migration-permissions).


- [ ] IAM permissions to view Activity Tracker instances and resources

- [ ] IAM permissions to read the DEK key name that is associated to a bucket if you have archiving configured on a bucket that has Key Protect enabled.

- [ ] IAM permissions to manage the Activity Tracker Event Routing service

- [ ] IAM permissions to create authorizations between services in the account

    - [ ] Activity Tracker Event Routing and Cloud Logs

    - [ ] Cloud Logs and Cloud Object Storage

    - [ ] Cloud Logs and IBM Cloud Event Notifications

- [ ] IAM permissions to create buckets

- [ ]  IAM permissions to configure alert destinations in IBM Cloud Event Notifications

## Migration
{: #template-migration-at-3}

- [ ] Identify the regions where you have provisioned Activity Tracker instances.

For each instance, complete the following steps:

### Step 1. Run the Migration tool command
{: #template-migration-at-3-1}

You can run the migration tool as follows:

Run the Migration Tool in a development or staging environment to test and validate the migration.
{: important}

- [ ] Run the migration tool

    ```sh
    ibmcloud logging migrate create-resources --scope instance --instance-crn CRN_VALUE --platform --ingestion-key INGESTION_KEY [--instance-name INSTANCENAME] [--instance-resource-group-id RESOURCEGROUPID] [--api]|[-t -f]
    ```
    {: codeblock}

    Add `--api` to migrate and create resources.

    Add `-t -f` to generate Terraform files and apply creation of resources. You are asked to confirm that you want to apply the scripts.

    You can add a new name for the instance that is created in Cloud Logs by adding the option `--instance-name INSTANCENAME`.

    You can change the resource group ID associated with the instance that is created in Cloud Logs by adding the option `--instance-resource-group-id RESOURCEGROUPID`.

    For more information, see [Migrating Activity Tracker instances](/docs/cloud-logs?topic=cloud-logs-migration-atracker-n-cl).

    This command will:

    - [ ] Migrate the instance, its resources such as views, dashboards, screens, alerts and exclusion rules.

    - [ ] Check if archiving is enabled and migrate the archiving configuration. If it is, as part of the migration, 2 buckets are created to collect data and metrics.

    - [ ] Add IAM authroizations

        - [ ] Add an IAM authorization between Cloud Logs and the data bucket.

        - [ ] Add an IAM authorization between Cloud Logs and the metrics bucket.

        - [ ] Add an IAM authorization between Cloud Logs and the Event Notifications service.

        - [ ]  Add an external integration in Cloud Logs to the Event Notifications instance.

    - [ ] Migrate notification channels (Slack, PagerDuty, WebHook) by creating the resources (topics, destinations, and subscriptions) to trigger alerts trough this channels in the IBM CLoud Events Notifications service.

    - [ ] Configure Activity Tracker Event Routing (to continue receiving auditing events in the Activity Tracker instance and the new Cloud Logs instance.)

        - [ ]  Create a target with the details of the Activity Tracker instance.

        - [ ]  Create a target with the details of the Cloud Logs instance

        - [ ]  Create a route with a rule to route auditing events of the region being migrated to the Activity Tracker target.

        - [ ]  Create a route with a rule to route auditing events of the region being migrated to the Cloud Logs target.

        - [ ]  Add some default dashboards, parsing rules, and views to manage auditing events.

    - [ ] IAM policies that apply to users, service IDs, trusted profiles, and access groups are migrated.

The Migration Tool only migrates configuration of selected resources.
{: important}

### Step 2. Manual tasks
{: #template-migration-at-3-2}

Complete the following manual tasks:

- [ ] IAM: For API keys (service ID / user ID), you need to recreate them and modify the applications that use it so they include permissions to work with the new services and resources.

- [ ] If you have parsing rules configured in the Activity Tracker instance, you must manually recreate them in Cloud Logs. (In Cloud Logs, you must use Regex to parse the data.) For more information, see [Extracting specific values as JSON keys](/docs/cloud-logs?topic=cloud-logs-parse-extract-rule).

- [ ] If you have log groups configured, you must manually migrate them to data access rules.

- [ ] If you have streaming configured, you must manually migrate the configuration. For more information, see [Streaming data](/docs/cloud-logs?topic=cloud-logs-streaming).

- [ ] Modify any runbooks for DevOps
