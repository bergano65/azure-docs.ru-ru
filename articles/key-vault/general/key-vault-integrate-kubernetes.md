---
title: Интеграция Azure Key Vault с Kubernetes
description: В рамках этого учебника вы получите доступ к секретам Azure Key Vault с помощью драйвера интерфейса хранилища контейнера (CSI) хранилища секретов, а затем подключитесь к объектам pod Kubernetes.
author: ShaneBala-keyvault
ms.author: sudbalas
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/25/2020
ms.openlocfilehash: bfcaf9d4b1d03457f2e4cddd2e0eaf9d9d58eee2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869190"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>Руководство по настройке и запуску поставщика Azure Key Vault для драйвера CSI хранилища секретов в Kubernetes

> [!IMPORTANT]
> Драйвер CSI — это проект с открытым исходным кодом, и он не поддерживается службой технической поддержки Azure. Все отзывы и сведения об ошибках, связанные с интеграцией Key Vault с драйвером CSI, отправляйте по ссылке на сайт GitHub, приведенной внизу на этой странице. Это средство предоставляется пользователям для самостоятельной установки на кластерах и сбора отзывов представителей сообщества.

В рамках этого учебника вы получите доступ к секретам Azure Key Vault с помощью драйвера интерфейса хранилища контейнера (CSI) хранилища секретов, а затем подключите секреты к объектам pod Kubernetes.

В этом руководстве описано следующее:

> [!div class="checklist"]
> * Создание субъекта-службы или использование управляемых удостоверений.
> * Развертывание кластера Службы Azure Kubernetes (AKS) с помощью Azure CLI.
> * Установка Helm и драйвера CSI хранилища секретов.
> * Создание хранилища ключей Azure и задание секретов.
> * Создание собственного объекта SecretProviderClass.
> * Назначение субъекта-службы или использование управляемых удостоверений.
> * Развертывание pod с подключенными секретами из хранилища ключей.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

* Перед началом работы с этим руководством установите [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-a-service-principal-or-use-managed-identities"></a>Создайте участника-службы или используйте управляемые удостоверения

Если вы планируете использовать управляемые удостоверения, можно перейти к следующему разделу.

Создайте субъект-службу для управления доступом к ресурсам из Azure Key Vault. Доступ к этому субъекту-службе ограничен назначенными ему ролями. Эта функция позволяет контролировать, как субъект-служба может управлять вашими секретами. В следующем примере имя субъекта-службы — *contosoServicePrincipal*.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
Эта операция возвращает ряд пар "ключ — значение".

![Снимок экрана с идентификатором приложения и паролем для contosoServicePrincipal](../media/kubernetes-key-vault-1.png)

Скопируйте **идентификатор приложения** и **пароль** для последующего использования.

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Развертывание кластера Службы Azure Kubernetes (AKS) с помощью Azure CLI

Вам не нужно использовать Azure Cloud Shell. Командной строки (терминала) с установленным Azure CLI будет достаточно. 

Выполните действия в разделах "Создание группы ресурсов", "Создание кластера AKS" и "Подключение к кластеру" из [краткого руководства по развертыванию кластера Службы Azure Kubernetes с помощью Azure CLI](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough). 

> [!NOTE] 
> Если вы планируете использовать удостоверение pod вместо субъекта-службы, обязательно включите его при создании кластера Kubernetes, как показано в следующей команде:
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [Задайте переменную среды PATH](https://www.java.com/en/download/help/path.xml) для скачанного файла *kubectl.exe*.
1. Проверьте версию Kubernetes с помощью приведенной ниже команды, которая выводит версию клиента и сервера. Версия клиента — это установленный файл *kubectl.exe*, а версия сервера — это Служба Azure Kubernetes (AKS), в которой выполняется кластер.
    ```azurecli
    kubectl version
    ```
1. Убедитесь, что используется версия Kubernetes 1.16.0 или более поздняя. Приведенная ниже команда обновит кластер Kubernetes и пул узлов. Выполнение команды может занять несколько минут. В этом примере группа ресурсов — *contosoResourceGroup*, а кластер Kubernetes — *contosoAKSCluster*.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Отобразите метаданные созданного кластера AKS с помощью приведенной ниже команды. Скопируйте значения **principalId**, **clientId**, **subscriptionId** и **nodeResourceGroup** для последующего использования. Если кластер ASK не был создан с включенными управляемыми удостоверениями, **principalId** и **clientId** будут иметь значение NULL. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    В выходных данных выделены оба параметра.
    
    ![Снимок экрана Azure CLI с выделенными значениями principalId и clientId](../media/kubernetes-key-vault-2.png) ![Снимок экрана Azure CLI с выделенными значениями subscriptionId и nodeResourceGroup](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Установка драйвера CSI Helm и хранилища секретов.

Чтобы установить драйвер CSI хранилища секретов, сначала необходимо установить [Helm](https://helm.sh/docs/intro/install/).

Интерфейс драйвера [CSI хранилища секретов](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) позволяет получать секреты, хранящиеся в экземпляре Azure Key Vault, и использовать интерфейс драйвера для подключения содержимого этих секретов к объектам pod в Kubernetes.

1. Убедитесь, что для Helm установлена версия 3 или более поздняя версия.
    ```azurecli
    helm version
    ```
1. Установите драйвер CSI хранилища секретов и поставщик Azure Key Vault для драйвера:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Создание Azure Key Vault и задание секретов.

Чтобы создать собственное хранилище ключей и задать секреты, выполните инструкции из [краткого руководства по настройке и получении секрета из Azure Key Vault с помощью Azure CLI](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli).

> [!NOTE] 
> Вам не нужно использовать Azure Cloud Shell или создавать новую группу ресурсов. Вы можете использовать группу ресурсов, созданную ранее для кластера Kubernetes.

## <a name="create-your-own-secretproviderclass-object"></a>Создание собственного объекта SecretProviderClass

Используйте этот [шаблон](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/test/bats/tests/azure_v1alpha1_secretproviderclass.yaml), чтобы создать собственный пользовательский объект SecretProviderClass с заданными поставщиком параметрами для драйвера CSI хранилища секретов. Этот объект предоставит доступ к удостоверениям хранилища ключей.

В файле YAML SecretProviderClass введите отсутствующие параметры. Требуются следующие параметры.

* **userAssignedIdentityID:** идентификатор клиента субъекта-службы
* **KeyVaultName:** имя хранилища ключей.
* **objects:** контейнер для содержимого секрета, которое необходимо подключить.
    * **objectName:** имя содержимого секрета.
    * **objectType:** тип объекта (секрет, ключ, сертификат).
* **resourceGroup**. Имя группы ресурсов.
* **subscriptionId**: идентификатор подписки для хранилища ключей.
* **tenantID:** идентификатор клиента или идентификатор каталога хранилища ключей.

Обновленный шаблон показан в следующем коде. Скачайте его как файл YAML и заполните обязательные поля. В этом примере хранилище ключей — **contosoKeyVault5**. Оно содержит два секрета: **secret1** и **secret2**.

> [!NOTE] 
> Если вы используете управляемые удостоверения, укажите для параметра **usePodIdentity** значение *true* и задайте **userAssignedIdentityID** в виде пары кавычек ( **""** ). 

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
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If you're using a service principal, use the client id to specify which user-assigned managed identity to use. If you're using a user-assigned identity as the VM's managed identity, specify the identity's client id. If the value is empty, it defaults to use the system-assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the preceding command will return the client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the key vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                # [OPTIONAL for Azure] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name "contosoKeyVault5"
                                              #     the above command will display a list of secret names from your key vault
          objectType: secret                  # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the key vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the key vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the key vault
```
Ниже приведен вывод консоли для **az keyvault show --name contosoKeyVault5** с соответствующими выделенными метаданными.

![Снимок экрана с выходными данными консоли для az keyvault show --name contosoKeyVault5](../media/kubernetes-key-vault-4.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>Назначьте участника-службы или используйте управляемые удостоверения

### <a name="assign-a-service-principal"></a>Назначение субъекта-службы

Если вы используете субъект-службу, предоставьте ей разрешения на доступ к хранилищу ключей и извлечение секретов. Назначьте роль *Читатель* и предоставьте субъекту-службе разрешение *получать* секреты из хранилища ключей, выполнив следующую команду:

1. Назначьте субъект-службу имеющемуся хранилищу ключей. Параметр **$AZURE _CLIENT_ID** — это значение **appId**, скопированное после создания субъекта-службы.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    Выходные данные команды показаны на следующем изображении: 

    ![Снимок экрана со значением principalId](../media/kubernetes-key-vault-5.png)

1. Предоставьте субъекту-службе разрешения на получение секретов:
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    ```

1. Теперь для субъекта-службы настроено разрешение на чтение секретов из хранилища ключей. **$AZURE_CLIENT_SECRET** — это пароль субъекта-службы. Добавьте учетные данные субъекта-службы в качестве секрета Kubernetes, доступного с помощью драйвера CSI хранилища секретов:
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

> [!NOTE] 
> Если вы развертываете объект pod Kubernetes и получаете сообщение об ошибке с недопустимым идентификатором секрета клиента, возможно, истек срок действия идентификатора секрета клиента или он был сброшен. Чтобы устранить эту проблему, удалите секрет *secrets-store-creds* и создайте новый с текущим идентификатором секрета клиента. Чтобы удалить *secrets-store-creds*, используйте следующую команду:
>
> ```azurecli
> kubectl delete secrets secrets-store-creds
> ```

Если вы забыли идентификатор секрета клиента субъекта-службы, его можно сбросить с помощью следующей команды:

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="use-managed-identities"></a>Использование управляемых удостоверений

При использовании управляемых удостоверений назначьте определенные роли созданному кластеру AKS. 

1. Чтобы создать, вывести или прочесть назначаемое пользователем управляемое удостоверение, кластеру AKS необходимо назначить роль [оператора управляемого удостоверения](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator). Убедитесь, что **$clientId** является идентификатором клиента кластера Kubernetes. Для этой области он будет указан в службе подписки Azure, а именно в группе ресурсов узла, которая была создана при создании кластера AKS. Эта область гарантирует, что назначенные ниже роли применяются только к ресурсам в такой группе. 

    ```azurecli
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. Установите удостоверение Azure Active Directory (Azure AD) в AKS.
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Создание удостоверение Azure AD. В выходных данных скопируйте **clientId** и **principalId** для последующего использования.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Назначьте учетной записи Azure AD, созданной на предыдущем шаге в хранилище ключей, роль *Читатель*, а затем предоставьте разрешения удостоверения для получения секретов из хранилища ключей. Используйте **clientId** и **principalId** из удостоверения Azure AD.
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>Разверните pod с подключенными секретами из хранилища ключей.

Чтобы настроить объект SecretProviderClass, выполните следующую команду:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-a-service-principal"></a>Использование субъекта-службы

Если вы используете субъект-службу, выполните следующую команду, чтобы развернуть Kubernetes объекты pod с помощью SecretProviderClass и зашифрованных учетных данных, настроенных ранее. Шаблоны развертывания
* [Для Linux.](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-inline-volume-service-principal.yaml)
* [Для Windows.](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml)

```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="use-managed-identities"></a>Использование управляемых удостоверений

Если вы используете управляемые удостоверения, создайте *AzureIdentity* в кластере, который ссылается на созданное ранее удостоверение. Затем создайте *AzureIdentityBinding*, который будет ссылаться на созданную AzureIdentity. Заполните параметры в следующем шаблоне и сохраните его как *podIdentityAndBinding.yaml*.  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed service identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of the Azure AD identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
Выполните следующую команду для привязки:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Затем выполняется развертывание pod. Ниже приведен код файла развертывания YAML, который будет использовать привязку удостоверения pod из последнего шага. Сохраните этот файл как *podBindingDeployment.yaml*.

```yml
apiVersion: v1
kind: Pod
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

### <a name="check-the-pod-status-and-secret-content"></a>Проверка состояния pod и секретного содержимого 

Для просмотра развернутых объектов pod выполните следующую команду:
```azurecli
kubectl get pods
```

Чтобы проверить состояние объекта pod, используйте следующую команду:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Снимок экрана выходных данных Azure CLI с отображением состояния "Выполняется" для pod и всех событий как "Обычный" ](../media/kubernetes-key-vault-6.png)

В окне выходных данных развернутый объект pod должен находиться в состоянии *Выполняется*. В нижней части раздела **События** все типы событий отображаются как *Обычный*.

Убедившись, что объект pod работает, вы можете проверить, есть ли у него секреты из хранилища ключей.

Чтобы отобразить все секреты, содержащиеся в объекте pod, выполните следующую команду:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Чтобы отобразить содержимое определенного секрета, выполните следующую команду:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Убедитесь, что отображается содержимое секрета.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы обеспечить возможность восстановления хранилища ключей, см. следующие материалы:
> [!div class="nextstepaction"]
> [Включить обратимое удаление](https://docs.microsoft.com/azure/key-vault/general/soft-delete-cli)
