---
title: Используйте идентификацию AAD с помощью веб-сервиса
titleSuffix: Azure Machine Learning
description: Используйте aAD-идентификацию с помощью веб-сервиса в службе Azure Kubernetes для доступа к облачным ресурсам во время подсчета очков.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: f997aef59e91bed325b84af855a84f43cd639d83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122848"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Используйте иноеудостоверения Azure AD с помощью веб-службы машинного обучения в службе Azure Kubernetes

В этом виде, как вы узнаете, как назначить идентификацию Active Directory (AAD) для развернутой модели машинного обучения в службе Azure Kubernetes. Проект [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) позволяет приложениям безопасно получать доступ к облачным ресурсам с помощью AAD с помощью примитивов [управляемой идентификации](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) и Kubernetes. Это позволяет веб-службе безопасно получить доступ к ресурсам Azure без необходимости встраивать учетные данные или управлять токенами непосредственно внутри `score.py` скрипта. В этой статье объясняются шаги по созданию и установке идентификатора Azure в кластере службы Azure Kubernetes и присвоению идентификатора развернутому веб-службе.

## <a name="prerequisites"></a>Предварительные требования

- [Расширение Azure CLI для службы машинного обучения,](reference-azure-machine-learning-cli.md) [SDK для машинного обучения Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)или расширение кода Visual Studio [Azure Machine Learning.](tutorial-setup-vscode-extension.md)

- Доступ к кластеру AKS с помощью `kubectl` команды. Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)

- Веб-служба Azure Machine Learning, развернутая в кластере AKS.

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>Создание и установка идентификации Azure в кластере AKS

1. Чтобы определить, включен ли кластер AKS rBAC, используйте следующую команду:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Эта команда возвращает `true` значение включения RBAC. Это значение определяет команду для использования на следующем этапе.

1. Чтобы установить [aAD Pod Identity](https://github.com/Azure/aad-pod-identity#getting-started) в кластер akS, используйте одну из следующих команд:

    * Если кластер AKS **имеет включенную RBAC** используйте следующую команду:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * Если в кластере AKS **нет включенного RBAC,** используйте следующую команду:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        Выход команды аналогичен следующему тексту:

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. [Создайте итог Azure](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity) после шагов, показанных на странице проекта AAD Pod Identity.

1. [Установите итог Azure](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity) после шагов, показанных на странице проекта AAD Pod Identity.

1. [Установите связывание идентичности Azure](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding) после шагов, показанных на странице проекта AAD Pod Identity.

1. Если идентификация Azure, созданная на предыдущем этапе, не входит в ту же группу ресурсов, что и кластер AKS, следуйте [разрешениям Set Permissions для MIC](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) после шагов, показанных на странице проекта AAD Pod Identity.

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>Присвоить Итог Azure веб-службе машинного обучения

Следующие шаги используют идентификатор Azure, созданный в предыдущем разделе, и присваивают ее веб-службе AKS через **метку селектора.**

Во-первых, определите имя и пространство имени развертывания в кластере AKS, который необходимо назначить идентификатору Azure. Вы можете получить эту информацию, запустив следующую команду. Область имен должна быть вашим рабочим пространством Azure Machine Learning, а имя развертывания должно быть вашим конечным именем, как показано на портале.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Добавьте в развертывание метку селектора итога «Лазурный узел», отредактировав спецификацию развертывания. Значение селектора должно быть тем, которое вы определили в шаге 5 [установки связывания идентичности Azure.](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding)

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Отспособьте развертывание для добавления метки селектора итога Azure. Перейти к следующему разделу под `/spec/template/metadata/labels`. Вы должны увидеть такие `isazuremlapp: “true”`значения, как . Добавьте ярлык aad-pod-identity, как показано ниже.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
      - aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Чтобы убедиться, что метка была правильно добавлена, запустите следующую команду.

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

Чтобы увидеть все статусы стручка, запустите следующую команду.

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

После запуска стручков веб-службы для этого развертывания смогут получать доступ к ресурсам Azure через идентификатор Azure без необходимости встраивать учетные данные в код. 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>Назначить соответствующие роли вашей идентификации Azure

[Назначьте управляемое удостоверение Azure с соответствующими ролями](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) для доступа к другим ресурсам Azure. Убедитесь, что присваиваемые роли имеют правильные **действия данных.** Например, [в роли чтения данных хранилища Blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) будут считываться разрешения на хранение Blob, в то время как общая [роль чтения](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) может не быть.

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>Используйте Итог Azure с помощью веб-службы машинного обучения

Развертывание модели в кластер AKS. Скрипт `score.py` может содержать операции, указывающие на ресурсы Azure, к которым имеет доступ ими Azure Identity. Убедитесь, что вы установили необходимые зависимости библиотеки клиентов для ресурса, к который вы пытаетесь получить доступ. Ниже приведены несколько примеров того, как можно использовать итог Azure для доступа к различным ресурсам Azure из службы.

### <a name="access-key-vault-from-your-web-service"></a>Доступ к Key Vault из веб-сервиса

Если вы предоставили доступ к секрету Вашего **Убежища,** вы `score.py` можете получить к нему доступ с помощью следующего кода.

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name) 
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

### <a name="access-blob-from-your-web-service"></a>Доступ к Blob из веб-сервиса

Если вы предоставили доступ к данным Azure Identity, `score.py` в хранилище **Blob,** можно получить к нему доступ с помощью следующего кода.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>Дальнейшие действия

* Для получения дополнительной информации о том, как использовать клиентскую библиотеку Python Azure Identity, можно [ознакомиться с репозиторием](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) gitHub.
* Подробное руководство по развертыванию моделей в кластерах служб [how-to](how-to-deploy-azure-kubernetes-service.md)Azure Kubernetes можно, см.