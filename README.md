# Como configurar las credenciales de AWS en github

Este post describe como configurar las credenciales de aws usando GitHub's OIDC provider (AssumeRoleWithWebIdentity) la cual es una de las 5 formas de configurar las credenciales y la recomendada por AWS.

# Paso 1: Crear un proveedor de identidad (Identity provider IDP OIDC)

# Configure Envionmets in Github

Ve a la seccion de
Go to Github create desired environments (pro) or use secrets and variables

{EVN}\_AWS_GITHUB_ROLE = arn of the role created in previous step
{EVN}\_AWS_REGION = desired region
Create github action using these secrets / variables

# Security recommendations

We recommend following Amazon IAM best practices for the AWS credentials used in GitHub Actions workflows, including:

Do not store credentials in your repository's code.
Grant least privilege to the credentials used in GitHub Actions workflows. Grant only the permissions required to perform the actions in your GitHub Actions workflows. Do not assume overly permissive roles, even for testing.
Monitor the activity of the credentials used in GitHub Actions workflows.
Use temporary credentials when possible.
Periodically rotate any long-term credentials you use.
