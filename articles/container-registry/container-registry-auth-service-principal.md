---
title: Authenticate with service principal
description: Предоставление доступа к образам, размещенным в закрытом реестре контейнеров, с помощью субъекта-службы Azure Active Directory.
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 37da784c8e95a5f5b924532e4a019552924a1a3f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455407"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Аутентификация в реестре контейнеров Azure с помощью субъектов-служб

С помощью субъекта-службы Azure Active Directory (Azure AD) вы можете предоставить образу контейнера доступ `docker push` и `pull` к реестру контейнеров. Субъект-служба позволяет предоставлять автоматический доступ к службам и приложениям.

## <a name="what-is-a-service-principal"></a>Что такое субъект-служба?

*Субъекты-службы* в Azure AD используются для доступа к ресурсам Azure в подписке. You can think of a service principal as a user identity for a service, where "service" is any application, service, or platform that needs to access the resources. Субъекту-службе можно присвоить права доступа, ограниченные конкретными ресурсами. После этого вам останется лишь настроить применение этого субъекта-службы для доступа к ресурсам из приложения или службы.

В контексте реестра контейнеров Azure субъект-служба Azure AD может получать разрешения на получение, на передачу и получение или другие права для частного реестра в Azure. Полный список см. в статье, посвященной [ролям и разрешениям реестра контейнеров Azure](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Почему именно субъект-служба?

Субъект-служба Azure AD позволяет предоставить ограниченный доступ к закрытому реестру контейнеров. Create different service principals for each of your applications or services, each with tailored access rights to your registry. Кроме того, вы сможете в любой момент легко изменить реквизиты или отменить доступ для каждого конкретного субъекта-службы, поскольку его учетные данные применяются только в одном приложении или службе.

For example, configure your web application to use a service principal that provides it with image `pull` access only, while your build system uses a service principal that provides it with both `push` and `pull` access. If development of your application changes hands, you can rotate its service principal credentials without affecting the build system.

## <a name="when-to-use-a-service-principal"></a>Когда следует использовать субъект-службу

Субъект-службы следует очень удобны для предоставления доступа к реестру при работе со **сценариями с автоматическим доступом**. Это позволяет приложениям, службам и (или) скриптам передавать или получать образы контейнеров в полностью автоматическом режиме или без участия оператора. Пример.

  * *Pull*: Deploy containers from a registry to orchestration systems including Kubernetes, DC/OS, and Docker Swarm. You can also pull from container registries to related Azure services such as [Azure Kubernetes Service (AKS)](../aks/cluster-container-registry-integration.md), [Azure Container Instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/), and others.

  * *Push*: Build container images and push them to a registry using continuous integration and deployment solutions like Azure Pipelines or Jenkins.

For individual access to a registry, such as when you manually pull a container image to your development workstation, we recommend using your own [Azure AD identity](container-registry-authentication.md#individual-login-with-azure-ad) instead for registry access (for example, with [az acr login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Примеры скриптов

You can find the preceding sample scripts for Azure CLI on GitHub, as well as versions for Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Authenticate with the service principal

Once you have a service principal that you've granted access to your container registry, you can configure its credentials for access to "headless" services and applications, or enter them using the `docker login` command. Используйте следующие значения:

* **User name** - service principal application ID (also called *client ID*)
* **Password** - service principal password (also called *client secret*)

Each value is a GUID of the form `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

> [!TIP]
> Можно создать пароль субъекта-службы повторно, выполнив команду [az ad sp reset-credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset).
>

### <a name="use-credentials-with-azure-services"></a>Use credentials with Azure services

You can use service principal credentials from any Azure service that authenticates with an Azure container registry.  Use service principal credentials in place of the registry's admin credentials for a variety of scenarios.

For example, use the credentials to pull an image from an Azure container registry to [Azure Container Instances](container-registry-auth-aci.md).

### <a name="use-with-docker-login"></a>Use with docker login

You can run `docker login` using a service principal. In the following example, the service principal application ID is passed in the environment variable `$SP_APP_ID`, and the password in the variable `$SP_PASSWD`. For best practices to manage Docker credentials, see the [docker login](https://docs.docker.com/engine/reference/commandline/login/) command reference.

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Once logged in, Docker caches the credentials.

### <a name="use-with-certificate"></a>Use with certificate

If you've added a certificate to your service principal, you can sign into the Azure CLI with certificate-based authentication, and then use the [az acr login][az-acr-login] command to access a registry. Using a certificate as a secret instead of a password provides additional security when you use the CLI. 

A self-signed certificate can be created when you [create a service principal](/cli/azure/create-an-azure-service-principal-azure-cli). Or, add one or more certificates to an existing service principal. For example, if you use one of the scripts in this article to create or update a service principal with rights to pull or push images from a registry, add a certificate using the [az ad sp credential reset][az-ad-sp-credential-reset] command.

To use the service principal with certificate to [sign into the Azure CLI](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal), the certificate must be in PEM format and include the private key. If your certificate isn't in the required format, use a tool such as `openssl` to convert it. When you run [az login][az-login] to sign into the CLI using the service principal, also provide the service principal's application ID and the Active Directory tenant ID. The following example shows these values as environment variables:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Then, run [az acr login][az-acr-login] to authenticate with the registry:

```azurecli
az acr login --name myregistry
```

The CLI uses the token created when you ran `az login` to authenticate your session with the registry.

## <a name="next-steps"></a>Дальнейшие действия

* See the [authentication overview](container-registry-authentication.md) for other scenarios to authenticate with an Azure container registry.

* For an example of using an Azure key vault to store and retrieve service principal credentials for a container registry, see the tutorial to [build and deploy a container image using ACR Tasks](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
