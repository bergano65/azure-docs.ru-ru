---
title: Интеграция Azure Key Vault с Kubernetes
description: В этом руководстве вы получите доступ к секретам Azure Key Vault с помощью драйвера "Секреты хранилища CSI (интерфейс хранилища контейнера)", а затем подключитесь к объекту Kubernetes Pod.
author: taytran0
ms.author: t-trtr
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/04/2020
ms.openlocfilehash: e945a30ca1fcd62fdfccd16d4e853540dbf73d8a
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636938"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-secret-store-csi-driver-on-kubernetes"></a>Руководство по настройке и запуску поставщика Azure Key Vault для секретного хранилища CSI Driver в Kubernetes

В этом руководстве вы получите доступ к секретам Azure Key Vault с помощью драйвера "Секреты хранилища CSI (интерфейс хранилища контейнера)", а затем подключитесь к объекту Kubernetes Pod.

В этом руководстве описано следующее:

> [!div class="checklist"]
> * Создание субъекта-службы
> * Развертывание кластера Службы Azure Kubernetes
> * Установка драйвера CSI Helm и хранилища секретов
> * Создание Azure Key Vault и задание секретов
> * Создание собственного объекта SecretProviderClass
> * Развертывание Pod с подключенными секретами из Key Vault

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

Перед началом работы с этим руководством установите [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-a-service-principal-or-use-managed-identities"></a>Создайте участника-службы или используйте управляемые удостоверения

Если вы планируете использовать управляемые удостоверения, можно перейти к следующему разделу.

Создайте субъект-службу для управления доступом к ресурсам из Azure Key Vault. Доступ к этому субъекту-службе ограничен назначенными ему ролями. Эта функция позволяет контролировать, как субъект-служба может управлять вашими секретами. В следующем примере имя субъекта-службы — **contosoServicePrincipal**.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
Эта операция возвращает ряд пар "ключ — значение":

![Образ —](../media/kubernetes-key-vault-1.png)

Запишите значения параметров **appId** и **password**. Эти учетные данные понадобятся позже.



## <a name="deploy-an-azure-kubernetes-service-cluster-using-azure-cli"></a>Разверните кластер Службы Azure Kubernetes с помощью Azure CLI

Не нужно использовать Azure Cloud Shell, ваша командная строка (терминал) с установленным Azure CLI подойдет. 

Следуйте указаниям в этом [руководстве](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) и выполните следующие действия. **Создайте группу ресурсов**, **создайте кластер AKS** и **подключитесь к кластеру**.

**Примечание.** Если вы планируете использовать удостоверение Pod вместо субъекта-службы. Обязательно включите его при создании кластера Kubernetes, как показано ниже:

```azurecli
az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
```

1. [Задайте для переменной среды PATH](https://www.java.com/en/download/help/path.xml) файл "kubectl. exe", который был скачан.
1. Проверьте версию Kubernetes, выполнив приведенную ниже команду. Эта команда выводит версию клиента и сервера. Версия клиента — это установленный «kubectl.exe», в то время как серверная версия — это службы Azure Kubernetes, на которых выполняется кластер.
    ```azurecli
    kubectl version
    ```
1. Убедитесь, что используется версия Kubernetes **1.16.0** или более поздняя. Эта команда обновит кластер Kubernetes и пул узлов. На полный перезапуск может потребоваться несколько минут. В этом примере группа ресурсов — **contosoResourceGroup**, а кластер Kubernetes — **contosoAKSCluster**.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Отобразите метаданные созданного кластера AKS с помощью приведенной ниже команды. Скопируйте **principalId**, **clientId**, **subscriptionId** и **nodeResourceGroup**.
    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    Это выходные данные с выделенными обоими параметрами.
    
    ![Изображение](../media/kubernetes-key-vault-5.png) ![Изображение](../media/kubernetes-key-vault-6.png)
    
## <a name="install-helm-and-secrets-store-csi-driver"></a>Установка драйвера CSI Helm и хранилища секретов

Необходимо установить [Helm](https://helm.sh/docs/intro/install/), чтобы установить драйвер Secrets Store CSI.

Интерфейс драйвера [Secrets Store CSI](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) позволяет получать конфиденциальные данные, хранящиеся в экземпляре Azure Key Vault, и использовать интерфейс драйвера для подключения этих конфиденциальных данных к Kubernetes Pod.

1. Проверьте версию Helm и убедитесь, что она имеет значение v3 или выше:
    ```azurecli
    helm version
    ```
1. Установите драйвер CSI хранилища секретов и поставщик Azure Key Vault для драйвера:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-secrets"></a>Создание Azure Key Vault и задание секретов

Следуйте этому [руководству](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli), чтобы создать собственный Key Vault и задать секреты.

**Примечание.** Вам не нужно использовать Azure Cloud Shell или создавать новую группу ресурсов. Можно использовать группу ресурсов, созданную ранее для кластера Kubernetes.

## <a name="create-your-own-secretproviderclass-object"></a>Создание собственного объекта SecretProviderClass

Используйте этот [шаблон](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass.yaml), чтобы создать собственный пользовательский объект SecretProviderClass для предоставления параметров, относящихся к поставщику, для драйвера Secrets Store CSI. Этот объект предоставит доступ к удостоверениям Key Vault.

С помощью предоставленного файла Sample SecretProviderClass YAML. Вы будете заполнять недостающие параметры. Требуются следующие параметры.

1.  **userAssignedIdentityID:** идентификатор клиента субъекта-службы
1.  **KeyVaultName:** имя хранилища ключей
1.  **objects:** этот объект будет содержать все секретные данные, которые вы хотите подключить
    1.  **objectName:** имя секретного содержимого
    1.  **objectType:** тип объекта (секрет, ключ, сертификат)
1.  **resourceGroup:** имя группы ресурсов
1.  **subscriptionId:** идентификатор подписки для Key Vault
1.  **tenantID:** идентификатор клиента (т. е. идентификатор каталога) Key Vault

Ниже приведен обновленный шаблон, скачайте его как файл YAML и заполните соответствующие обязательные поля. В этом примере Key Vault — **contosoKeyVault5** и имеет два секрета: **secret1** и **secret2**.

**Примечание.** При использовании управляемых удостоверений поле **usePodIdentity** должно иметь значение **true** и оставить поле **userAssignedIdentityID** с кавычками **""** . 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If using a Service Principal, use the client id to specify which user assigned managed identity to use. If using a user assigned identity as the VM's managed identity, then specify the identity's client id. If empty, then defaults to use the system assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the above command will return the Client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the Key Vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the above command will displays the Key Vault metadata, which includes the subscription ID, resource group name, Key Vault 
    cloudName: ""                             # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name “contosoKeyVault5”
                                              #     the above command will display a list of secret names from your Key Vault
          objectType: secret                  # [REQUIRED] object types: secret, key or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the Key Vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the Key Vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the Key Vault
```
Ниже приведен вывод консоли для "az keyvault show --name contosoKeyVault5" с соответствующими выделенными метаданными.

![Образ —](../media/kubernetes-key-vault-2.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>Назначьте участника-службы или используйте управляемые удостоверения

### <a name="using-service-principal"></a>Использование субъект-службы

Если вы используете субъект-службу. Необходимо предоставить субъекту-службе разрешение на доступ к Key Vault и получение секретов. Назначьте роль **"Читатель"** и предоставьте субъекту-службе разрешение **"получить"** секреты из Key Vault, выполнив следующие действия:

1. Назначение субъекта-службы существующему Key Vault. Параметр **$AZURE _CLIENT_ID** — это **appId**, скопированный после создания субъекта-службы.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    Ниже представлен вывод команды: 

    ![Образ —](../media/kubernetes-key-vault-3.png)

1. Предоставьте субъекту-службе разрешения на доступ к Key Vault:
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    ```

1. Теперь, когда у субъекта-службы настроено разрешение на чтение секретов из Key Vault. **$AZURE_CLIENT_SECRET** — **пароль** субъекта-службы. Добавьте учетные данные субъекта-службы в качестве секрета Kubernetes, доступного с помощью драйвера Secrets Store CSI:
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

**Примечание.** Если позже возникает ошибка при развертывании модуля Kubernetes о недопустимом идентификаторе Client Secret ID. Возможно, имеется более старый идентификатор Client Secret ID, который просрочен или сброшен. Чтобы устранить эту проблему, удалите секреты "secrets-store-creds" и создайте новый с текущим идентификатором Client Secret ID. Чтобы удалить "secrets-store-creds", используйте следующую команду:
```azurecli
kubectl delete secrets secrets-store-creds
```

Если вы забыли идентификатор Client Secret ID субъекта-службы, его можно сбросить с помощью следующей команды:

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="using-managed-identities"></a>Использование управляемых удостоверений

При использовании управляемых удостоверений назначьте определенные роли кластеру AKS, который вы создали. 
1. Чтобы создать/вывести/прочесть назначаемое пользователем управляемое удостоверение, кластеру AKS должна быть назначена роль [участника управляемого удостоверения](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-contributor). Убедитесь, что **$clientId** относится к кластеру Kubernetes.

    ```azurecli
    az role assignment create --role "Managed Identity Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. Установите удостоверение Azure Active Directory (Azure AD) в AKS.
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Создание удостоверение Azure AD. Скопируйте **clientId** и **principalId**.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Назначьте роль читателя удостоверению Azure AD, которое вы только что создали для вашего Key Vault. Затем предоставьте удостоверению разрешение на получение секретов из Key Vault. Вы будете использовать **clientId** и **principalId** из только что созданного удостоверения Azure.
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-key-vault"></a>Развертывание Pod с подключенными секретами из Key Vault

В приведенной ниже команде будет настроен объект SecretProviderClass:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="using-service-principal"></a>Использование субъект-службы

Если вы используете субъект-службу. Приведенная ниже команда выполняет развертывание модулей Kubernetes Pod с настроенными вами SecretProviderClass и хранимыми вами учетными данными. Ниже приведен шаблон для развертываний [Linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-secrets-store-inline-volume-secretproviderclass.yaml) и [ Windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml).
```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="using-managed-identities"></a>Использование управляемых удостоверений

Использование управляемых удостоверений Вы создадите в вашем кластере **AzureIdentity**, который будет ссылаться на созданное ранее удостоверение. Затем создайте **AzureIdentityBinding**, который будет ссылаться на созданную **AzureIdentity**. Используя приведенный ниже шаблон, заполните соответствующие параметры и сохраните его как **podIdentityAndBinding.yaml**.  
```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure Idenity
spec:
    type: 0                                 # Set type: 0 for Managed Service Identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of your Azure Identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure Idenity
    selector: azure-pod-identity-binding-selector
```
    
Выполните следующую команду для привязки:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Далее следует реальное развертывание Pod. Ниже приведен файл развертывания YAML, который будет использовать привязку удостоверения Pod из последнего шага. Сохраните этот файл как **podBindingDeployment.yaml**.

```yml
kind: Pod
apiVersion: v1
metadata:
    name: nginx-secrets-store-inline
    labels:
    aadpodidbinding: azure-pod-identity-binding-selector
spec:
    containers:
    - name: nginx
        image: nginx
        volumeMounts:
        - name: secrets-store-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
    volumes:
    - name: secrets-store-inline
        csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
            secretProviderClass: azure-kvname
```

Выполните следующую команду, чтобы развернуть шаблон:

```azurecli
kubectl apply -f podBindingDeployment.yaml
```
### <a name="check-status-and-secret-content"></a>Проверка состояния и секретного содержимого 
Для просмотра развернутых модулей Pod:
```azurecli
kubectl get pods
```

Чтобы проверить состояние модуля, используйте следующую команду:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Образ —](../media/kubernetes-key-vault-4.png)

Развернутый модуль должен находиться в состоянии "Выполняется". В разделе "События" внизу все типы событий слева классифицируются как "Обычные".
Убедившись, что модуль работает, вы можете проверить, есть ли у модуля секреты из Key Vault.

Чтобы отобразить все секреты модуля, сделайте следующее.
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Чтобы получить содержимое из определенного секрета, выполните следующие действия.
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Проверьте содержимое отображаемого секрета.

## <a name="next-steps"></a>Дальнейшие действия

Убедитесь, что хранилище ключей можно восстановить:
> [!div class="nextstepaction"]
> [Включить обратимое удаление](https://docs.microsoft.com/azure/key-vault/general/soft-delete-clid)
