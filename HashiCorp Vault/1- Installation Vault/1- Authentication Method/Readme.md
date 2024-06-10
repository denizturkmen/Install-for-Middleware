# How to Create Policy & Role


### Enable the ```Userpass``` Authentication Method
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


### Enable the ```Approle``` Authentication Method
``` bash
# Enable the AppRole Authentication Method
vault auth enable approle

# Create a Vault policy on the vault machine
vault policy write approle-policy - <<EOF
path "secret/*" {
  capabilities = ["read"]
}
EOF

# Create a role with the appropriate policies:
vault write auth/approle/role/approle-role \
  token_policies="approle-policy" \
  token_ttl=1h \
  token_max_ttl=4h

# Retrieve the role ID and secret ID for the AppRole:
ROLE_ID=$(vault read -field=role_id auth/approle/role/approle-role/role-id)
SECRET_ID=$(vault write -f -field=secret_id auth/approle/role/approle-role/secret-id)

# show role_id and secret_id
echo $ROLE_ID
echo $SECRET_ID

# token - login
vault write auth/approle/login role_id="$ROLE_ID" secret_id="$SECRET_ID"
vault login token

# check
vault kv get secret/mysecret
==== Data ====
Key      Value
---      -----
value    my secret value


vault kv get secret/myapp
==== Data ====
Key      Value
---      -----
deniz    turkmen



```