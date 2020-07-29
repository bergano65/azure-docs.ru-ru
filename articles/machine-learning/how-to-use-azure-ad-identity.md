---
title: Использование удостоверения AAD с веб-службой
titleSuffix: Azure Machine Learning
description: Используйте удостоверение AAD с веб-службой в службе Kubernetes Azure для доступа к облачным ресурсам во время оценки.
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.date: 02/10/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: f76e149339e80ddeba8431afffbd677a4b595ec3
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319479"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Использование удостоверения Azure AD с веб-службой машинного обучения в службе Kubernetes Azure

В этом пошаговом окне вы узнаете, как назначить удостоверение Azure Active Directory (AAD) для развернутой модели машинного обучения в службе Azure Kubernetes. Проект [удостоверения Pod для AAD](https://github.com/Azure/aad-pod-identity) позволяет приложениям безопасно получать доступ к облачным ресурсам с помощью AAD, используя [управляемые удостоверения](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) и примитивы Kubernetes. Это позволяет веб-службе безопасно получать доступ к ресурсам Azure без необходимости внедрять учетные данные или управлять маркерами непосредственно внутри `score.py` скрипта. В этой статье объясняется, как создать и установить удостоверение Azure в кластере службы Azure Kubernetes и назначить удостоверение для развернутой веб-службы.

## <a name="prerequisites"></a>Предварительные требования

- [Расширение Azure CLI для службы машинное обучение](reference-azure-machine-learning-cli.md), [машинное обучение Azure SDK для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)или [расширение машинное обучение Azure Visual Studio Code](tutorial-setup-vscode-extension.md).

- Доступ к кластеру AKS с помощью `kubectl` команды. Дополнительные сведения см. в разделе [Подключение к кластеру](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster) .

- Машинное обучение Azure веб-службы, развернутой в кластере AKS.

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>Создание и установка удостоверения Azure в кластере AKS

1. Чтобы определить, включен ли в кластере AKS RBAC, используйте следующую команду:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Эта команда возвращает значение, `true` Если RBAC включен. Это значение определяет команду, которая будет использоваться на следующем шаге.

1. Чтобы установить [удостоверение для AAD Pod](https://github.com/Azure/aad-pod-identity#getting-started) в кластере AKS, выполните одну из следующих команд:

    * Если в кластере AKS **включен RBAC** , используйте следующую команду:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * Если в кластере AKS **не включен RBAC**, используйте следующую команду:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        Выходные данные команды похожи на следующий текст:

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. [Создайте удостоверение Azure](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity) , выполнив действия, показанные на странице проекта удостоверения Pod AAD.

1. [Установите удостоверение Azure](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity) , выполнив действия, показанные на странице проекта удостоверения Pod AAD.

1. [Установите привязку удостоверений Azure](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding) , выполнив действия, показанные на странице проекта удостоверения Pod AAD.

1. Если удостоверение Azure, созданное на предыдущем шаге, находится не в той же группе ресурсов, что и кластер AKS, следуйте указаниям в разделе [Настройка разрешений для MIC](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) , следуя шагам, приведенным на странице проекта удостоверений AAD Pod.

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>Назначение удостоверения Azure веб-службе машинного обучения

Следующие шаги используют удостоверение Azure, созданное в предыдущем разделе, и назначение его веб-службе AKS с помощью **метки Selector**.

Сначала укажите имя и пространство имен развертывания в кластере AKS, которому требуется назначить удостоверение Azure. Эти сведения можно получить, выполнив следующую команду. Пространства имен должны быть Машинное обучение Azure именем рабочей области, а имя развертывания должно быть именем конечной точки, как показано на портале.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Добавьте метку селектора удостоверения Azure в развертывание, изменив спецификацию развертывания. Значение селектора должно быть задано на шаге 5 [установки привязки удостоверений Azure](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding).

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Измените развертывание, чтобы добавить метку селектора удостоверений Azure. Перейдите к следующему разделу в разделе `/spec/template/metadata/labels` . Вы должны увидеть такие значения, как `isazuremlapp: “true”` . Добавьте метку AAD-Pod-Identity, как показано ниже.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
       aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Чтобы проверить правильность добавления метки, выполните следующую команду.

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

Чтобы просмотреть все состояния Pod, выполните следующую команду.

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

После того как модули Pod будут работать, веб-службы для этого развертывания смогут получить доступ к ресурсам Azure через удостоверение Azure без необходимости внедрять учетные данные в код. 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>Назначение соответствующих ролей удостоверению Azure

[Назначьте управляемое удостоверение Azure с соответствующими ролями](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) для доступа к другим ресурсам Azure. Убедитесь, что назначенные роли имеют правильные действия с **данными**. Например, [роль читателя данных BLOB-объекта хранилища](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) будет иметь разрешения на чтение BLOB-объекта хранилища, а универсальная [роль читателя](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) — нет.

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>Использование удостоверения Azure в веб-службе машинного обучения

Разверните модель в кластере AKS. `score.py`Скрипт может содержать операции, указывающие на ресурсы Azure, к которым имеет доступ удостоверение Azure. Убедитесь, что установлены необходимые зависимости клиентской библиотеки для ресурса, к которому вы пытаетесь получить доступ. Ниже приведено несколько примеров того, как можно использовать удостоверение Azure для доступа к различным ресурсам Azure из службы.

### <a name="access-key-vault-from-your-web-service"></a>Доступ к Key Vault из веб-службы

Если вы предоставили удостоверению Azure доступ на чтение к секрету в **Key Vault**, вы `score.py` можете получить к нему доступ, используя следующий код.

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

> [!IMPORTANT]
> В этом примере используется Дефаултазурекредентиал. Чтобы предоставить удостоверению доступ с помощью определенной политики доступа, см. [часть 4. получение секрета Azure Key Vault](../key-vault/general/authentication.md#part-4-retrieve-the-secret-from-your-azure-key-vault-in-an-application-python).

### <a name="access-blob-from-your-web-service"></a>Доступ к большому двоичному объекту из веб-службы

Если вы задаете удостоверению Azure доступ на чтение данных внутри **большого двоичного объекта хранилища**, вы `score.py` можете получить к нему доступ, используя следующий код.

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

* Дополнительные сведения об использовании клиентской библиотеки удостоверений Azure для Python см. в [репозитории](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) на сайте GitHub.
* Подробное руководство по развертыванию моделей в кластерах службы Kubernetes Azure [см. в](how-to-deploy-azure-kubernetes-service.md)этом разделе.
