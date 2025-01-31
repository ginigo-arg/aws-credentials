# Como configurar las credenciales de AWS en github

Este post describe como configurar las credenciales de aws usando GitHub's OIDC provider (AssumeRoleWithWebIdentity) la cual es una de las 5 formas de configurar las credenciales y la recomendada por AWS.

En mi caso utilizo esta implementacion para poder implementar CI/CD en mis proyectos y desplegar los recursos necesarios en AWS.

# Paso 1: Crear un proveedor de identidad (Identity provider IDP-OIDC)

![App Screenshot](/screenshots/1.png)

1. Dirigete al servicio IAM en la consola de AWS
2. Selecciona la opción "Proveedores de identidad" (Identity Providers)
3. Selecciona crear/agregar un proveedor

![App ScreenShot](/screenshots/2.png)

1. Selecciona la opcion de OpenId Connect
2. En el campo Url del proveedor ingresa la siguiente URL: https://token.actions.githubusercontent.com
3. En el campo de audience pega lo siguiente: sts.amazonaws.com
4. Recomiendo que agregues etiquetas para luego diferenciar facilmente el recurso dentro de la consola de AWS. Puede que tengas distintos provedores de identidad con nombres parecidos. Sin embargo este proveedor se puede reutilizar para multiples proyectos en _GITHUB_
5. Click en "Agregar Proveedor

![App Screenshot](/screenshots/3.png)
Una vez Agregado el proveedor en nuestro listado de proveedores te aparecera algo asi

![App Screenshot](/screenshots/4.png)
Si hacemos click en nuestro proveedor veremos algo asi.

1. Copiar el ARN del proveedor. Lo necesitaremos luego

# Paso 2: Configurar la relacion de confianza con github

Para utilizar el proveedor OIDC de GitHub, primero debe configurar la federación con el proveedor como IdP de IAM. El proveedor OIDC de GitHub solo debe crearse una vez por cuenta (es decir, varios roles de IAM que puede asumir el OIDC de GitHub pueden compartir un único proveedor OIDC).
Debajo te dejare una plantilla de CloudFormation que configurará esta confianza por ti.

Puedes descargar la plantilla de CloudFormation desde aqui: https://d38mtn6aq9zhn6.cloudfront.net/configure-aws-credentials-latest.yml

![App Screenshot](/screenshots/5.png)

1. Dirigete al servicio de CloudFormation en la consola de AWS
2. En crear pila (Stack) selecciona con "recursos nuevos"

![App Screenshot](/screenshots/6.png)

1. Selecciona crear con una plantilla existente
2. Selecciona cargar un archivo de plantilla (configure-aws-credentials-latest file)
3. Elegir el archivo descargado anteriormente.
4. Siguiente

![App Screenshot](/screenshots/7.png)

1. Aqui Debes especificar un nombre arbitrario con el que diferenciaras el stack

Ahora nos toca cargar todos los parametros que tiene la plantilla:

2. La organizacion de github siempre se encuntra luego del dominio de github en cualquiera de nuestro repositorio
   https://github.com/ _ginigo-arg_ /notes-with-sst
3. OIDC Audience debemos colocar: sts.amazonaws.com
4. Colocar el ARN de nuestro IDP creado anteriormente
5. Nombre del repositorio
   https://github.com/ginigo-arg/ _notes-with-sst_
6. Siguiente

![App Screenshot](/screenshots/8.png)
![App Screenshot](/screenshots/9.png)

1. Seleccionamos el checkbox para que aws pueda tener permisos sobre los recursos creados
2. Click en Siguiente

![App Screenshot](/screenshots/17.png)

1. Luego de revisar y darle al boton de crear veremos como se empiezan a crear todos los recursos descriptos en nuestra plantilla. Veras que pasa de tener status "CREATE_IN_PROGRESS" a "CREATE_COMPLETE"

- Con esto ya tenemos todo listo del lado de AWS.

# Configurar las variables de entorno en Github

![App Screenshot](/screenshots/15.png)

1. Configura las variables de entorno:
   - AWS_GITHUB_ROLE (ARN del role creado en el paso 2)
   - AWS_REGION (por defecto es us-east-1)

Para configurar las credenciales de AWS utiliza la siguiente accion: https://github.com/aws-actions/configure-aws-credentials/tree/v4/

# En mi archivo .YML

    # Configure AWS Credentials Based on Auth Method
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ env.AWS_GITHUB_ROLE }}
          aws-region: ${{ env.AWS_REGION }}
          role-duration-seconds: 1800

# Security recommendations

We recommend following Amazon IAM best practices for the AWS credentials used in GitHub Actions workflows, including:

Do not store credentials in your repository's code.
Grant least privilege to the credentials used in GitHub Actions workflows. Grant only the permissions required to perform the actions in your GitHub Actions workflows. Do not assume overly permissive roles, even for testing.
Monitor the activity of the credentials used in GitHub Actions workflows.
Use temporary credentials when possible.
Periodically rotate any long-term credentials you use.

## Autor

- [@ginigo-arg](https://www.github.com/ginigo-arg)

Espero te sirva. Saludos! 🙌
