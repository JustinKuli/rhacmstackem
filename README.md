# RHACMStackEm

Image to provision an OCP cluster from a ClusterPool with the latest RHACM deployed using [StartRHACM](https://github.com/dhaiducek/startrhacm)

Image URL: `quay.io/dhaiduce/rhacmstackem`

## CronJob Deployment

1. Pre-deployment setup
  - Set up a ServiceAccount in your ClusterPool namespace (see the [Lifeguard Service Account docs](https://github.com/open-cluster-management/lifeguard/blob/main/docs/creating_and_using_service_accounts_for_CI.md#creating-a-service-account-and-configuring-roles))
  - Use Quay to create a secret on the ClusterPool cluster with access to https://quay.io/organization/open-cluster-management
    - Navigate to your Account Settings: `https://quay.io/user/<username>?tab=settings`
    - Click "Generate Encrypted Password"
    - With "Kubernetes Secret" selected on the left, follow Step 1 and Step 2 to create the secret on the cluster (you can modify `metadata.name` as you wish)
  - Use GitHub to set up a [Personal Access Token](https://github.com/settings/tokens) with access to the private [Pipeline](https://github.com/open-cluster-management/pipeline/) repo
  - (Optional) Create a new private Slack channel in your workspace. In the channel, click the `i` to view the channel's details. Click the "More" button and select "Add apps". Add "ClusterPool Bot". The "Incoming Webhook" URL is the SLACK_URL you'll need to post to your channel.
2. Export environment variables:
  ```bash
  # REQUIRED EXPORTS
  export SERVICE_ACCOUNT_NAME="" # Service Account with permissions to perform actions on the cluster
  export CLUSTERPOOL_TARGET_NAMESPACE="" # Namespace on ClusterPool cluster
  export CLUSTERPOOL_NAME="" # Name of ClusterPool to use
  export CLUSTERCLAIM_GROUP_NAME="" # Name of RBAC group to give additional permissions
  export QUAY_SECRET_NAME="" # Name of the Quay secret on the ClusterPool cluster to deploy RHACM from step 1
  export GIT_USER="" # Git username with permissions for Pipeline
  export GIT_TOKEN="" # Git token with permissions for Pipeline

  # OPTIONAL EXPORTS
  export SLACK_URL="" # Slack URL to post cluster information to a channel
  export CLUSTERPOOL_RESIZE="" # Whether to allow increasing the ClusterPool size if no claims are available (default: "true)
  export CLUSTERPOOL_MAX_CLUSTERS="" # Maximum size of ClusterPool if resize is enabled (default: "5")
  export CLUSTERCLAIM_LIFETIME="" # Lifetime of claimed cluster (default: "10h")
  export AUTH_REDIRECT_PATHS="" # Oauth redirect paths in a space-separated string (default: "") Example: "/ /path1/ /path2/"
  ```
3. Change to the `deployment/` directory and run the `rhacmstackem_deployment.yaml.sh` script to create the necessary YAML files:
  ```bash
  cd deployment/
  ./rhacmstackem_deployment.yaml.sh
  ```
4. Make sure you're pointing to your ClusterPool cluster and run `oc apply -f .` to deploy the files.
