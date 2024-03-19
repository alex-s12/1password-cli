# 1Password CLI

## Getting Started

1. Install 1Password CLI
```bash
brew install 1password-cli
```

2. Get a list of your 1Password Vaults
```bash
op vaule list 
```
3. Secret references look like this, and can easily be grabbed from the 1Password UI

![img_2.png](images/img_2.png)
4. Easily copy secret references from the UI

![img_1.png](images/img_1.png)

## Method 1: `op read`
A convenience feature that doesn't mask the credential in any way. You can use op read with a secret reference to print the secret to stdout.

Example 1:
```bash
op read "op://Private/ssh keys/ssh key/private key?ssh-format=openssh"
```
Outputs the following to your terminal:
```text
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAACmFlczI1Ni1jdHIAAAAGYmNyeXB0AAAAGAAAABD3rRrf8J
ruD0CxZTYfpbTYAAAAEAAAAAEAAAAzAAAAC3NzaC1lZDI1NTE5AAAAIJ5B/GnxX6t9jMwQ
G7QE7r5daJLkMKTZhNZhWfvzK2y+AAAAkLgQAivYu/+12/YrZhK5keIAZf4ZgsZsZ2JI2q
qbx23PqgO93oGy1iCxXe3kngQL4cM6lwOZPsZPKCinkN6KxEr6RnXqFRHJbMpOiGeZhTuD
rjeo77HqFdxDqDeckB77XCKL0Ew28H5JlM/WO31XR3Z4VBAgTe+BQLjrFV8WU5UX38hpBJ
PMJyRsK72ZUDDaGQ==
-----END OPENSSH PRIVATE KEY-----
```

Example 2:
Use `op read` in bash scripts instead of hard-coding your credentials for convenience scripts
```bash
#!/bin/bash

docker login -u $(op read op://prod/docker/username) \
-p $(op read op://prod/docker/password)
```

## Method 2: `op run`
You can set environment variables to secret references, then use op run to pass secrets to an application or script at runtime.

For this example, go and select a credential from your 1Password

1. 1Password will conceal the credentials
```bash
op run --env-file .env python3 script.py
```
... but masking can be removed as well with the `--no-masking` flag:
```bash
op run --no-masking --env-file .env python3 script.py
```

## Method 3: `op inject`
You can use `op inject` to replace secret references in a script or file with the secrets they reference.

```bash
echo "here is my GitHub token: op://development/GitHub/credentials/personal_token" | op inject
```

Use `op inject` with a shared credential manager to store credential variables in configuration files

```yaml
# config.yml
database:
    host: http://localhost
    port: 5432
    username: op://prod/mysql/username
```
...this can safely be saved to source control, and a copy with credentials injected can be used locally with the following command:

```bash
op inject --in-file config.yml --out-file config.yml
```