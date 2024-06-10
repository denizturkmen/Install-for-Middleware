# How to Create Policy & Role


Enable the ```Userpass``` Authentication Method
``` bash
# Log in to Vault: Make sure you are logged into Vault with sufficient privileges.
vault login <your-token>

# Enable the Userpass Auth Method: Use the Vault CLI to enable the userpass authentication method.
vault auth enable userpass

# Create a Policy File: Write a policy file (e.g., example-policy.hcl). Policies define what a user or application can do within Vault.
vim example-policy.hcl
path "secret/*" {
  capabilities = ["create", "read", "update", "delete", "list"]
}

# Write the Policy to Vault: Use the Vault CLI to write the policy.
vault policy write example-policy example-policy.hcl

# Create a User with a Password: Use the vault write command to create a user with the userpass auth method and associate it with the policy created.
vault write auth/userpass/users/example-user password="example-password" policies="example-policy"


# Login as the User: Use the Vault CLI to log in as the newly created user.
vault login -method=userpass username=example-user password=example-password 

# login via token
vault token
hvs.CAESIA_3Il8BOm2sCq7CEqViReOoLo6HXKZvzmUfUag8ANY8Gh4KHGh2cy41ZE1rSjc5emlOYjhEWlJ0MTRHVmV2MHg

# Verify the User’s Access. Write a secret
vault kv put secret/mysecret value="my secret value"

# Read the secret
vault kv get secret/mysecret

```