---
title: "Implement Secure Azure Solutions"
date: 2023-08-11T14:31:58+02:00
draft: false
---

# Implement secure Azure solutions

## Implement Azure Key Vault

- Secrets Management
- Key Management
- Certificate Management

## Best practicies

### Authentication

- Managed identities for Azure resources: When you deploy an app on a virtual machine in Azure, you can assign an identity to your virtual machine that has access to Key Vault. You can also assign identities to other Azure resources. The benefit of this approach is that the app or service isn't managing the rotation of the first secret. Azure automatically rotates the service principal client secret associated with the identity. We recommend this approach as a best practice.

- Service principal and certificate: You can use a service principal and an associated certificate that has access to Key Vault. We don't recommend this approach because the application owner or developer must rotate the certificate.

- Service principal and secret: Although you can use a service principal and a secret to authenticate to Key Vault, we don't recommend it. It's hard to automatically rotate the bootstrap secret that's used to authenticate to Key Vault.

## Azure Key Vault best practices

- Use separate key vaults: Recommended using a vault per application per environment (Development, Pre-Production and Production). This pattern helps you not share secrets across environments and also reduces the threat if there is a breach.

- Control access to your vault: Key Vault data is sensitive and business critical, you need to secure access to your key vaults by allowing only authorized applications and users.

- Backup: Create regular back ups of your vault on update/delete/create of objects within a Vault.

- Logging: Be sure to turn on logging and alerts.

- Recovery options: Turn on soft-delete and purge protection if you want to guard against force deletion of the secret.

## Authenticate to Azure Key Vault

works with Azure Active Directory 

How to get service principal:
- Enable system-assigned *managed identity* (recommended!)
- register app with Azure AD tenant


### Authentication to Key Vault in application code

https://learn.microsoft.com/en-us/dotnet/api/overview/azure/identity-readme?view=azure-dotnet
https://learn.microsoft.com/en-us/azure/key-vault/general/developers-guide


### Authentication to Key Vault with REST

### Azure CLI

```
az keyvault create --name $myKeyVault --resource-group az204-vault-rg --location $myLocation
```

```
az keyvault secret set --vault-name $myKeyVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

```
az keyvault secret show --name "ExamplePassword" --vault-name $myKeyVault
```

uses TSL

## Implement managed identities

