# Using Secrets in tasks

Sample to build and run an image using secrets from Key Vaults



```sh
#environment variable
# export REGISTRY=myregistry
# export KEY_VAULT_NAME=myvault
# export KEY_VAULT_RESOURCE_GROUP=myRG

az configure --defaults acr=$REGISTRY
```

## Setup the task

```sh
az acr task create --name task-secret  \
    --context https://github.com/sajayantony/task-secrets.git \
    --file acb.yaml \
    --commit-trigger-enabled false \
    --assign-identity \
    --set KeyVault=$KEY_VAULT_NAME
```

## Obtain the principal ID for the task

```sh
principalID=$(az acr task show --name task-secret --query identity.principalId --output tsv)
```

## Setup permissions on KV

```sh
az keyvault set-policy --name $KEY_VAULT_NAME \
    -g $KEY_VAULT_RESOURCE_GROUP \
    --object-id $principalID \
    --secret-permissions get
```

## Execute the task

```sh
az acr task run -n task-secret
```
