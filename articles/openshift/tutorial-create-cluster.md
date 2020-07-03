---
title: Руководство по созданию кластера Azure Red Hat OpenShift 4
description: Узнайте, как создать кластер Microsoft Azure Red Hat OpenShift с помощью Azure CLI.
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 32069d9594d4579bd18ec3fd0e76af7bdc69f4d0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232161"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Учебник. Создание кластера Azure Red Hat OpenShift 4

В этом руководстве (часть 3) вы подготовите среду для создания кластера Azure Red Hat OpenShift с OpenShift 4 и создания кластера. Вы научитесь:
> [!div class="checklist"]
> * Настройте необходимые компоненты и создайте необходимую виртуальную сеть и подсети.
> * Развертывание кластера

## <a name="before-you-begin"></a>Подготовка к работе

Если вы решили установить и использовать CLI локально, для работы с этим руководством вам потребуется Azure CLI версии 2.0.75 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="install-the-az-aro-extension"></a>Установка `az aro` расширения
`az aro` Расширение позволяет создавать, получать доступ и удалять кластеры OpenShift для Azure Red Hat непосредственно из командной строки с помощью Azure CLI.

Выполните следующую команду, чтобы установить `az aro` расширение.

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

Если расширение уже установлено, его можно обновить, выполнив следующую команду.

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>Регистрация поставщика ресурсов

Затем необходимо зарегистрировать поставщик `Microsoft.RedHatOpenShift` ресурсов в подписке.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

Убедитесь, что расширение зарегистрировано.

```azurecli-interactive
az -v
```

  Вы должны получить выходные данные, аналогичные приведенным ниже.

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Получение секрета по запросу Red Hat (необязательно)

Секретный ключ Red Hat позволяет вашему кластеру получать доступ к реестрам контейнеров Red Hat вместе с дополнительным содержимым. Этот шаг необязателен, но мы рекомендуем его выполнить.

Получите секрет опрашивающего запроса, перейдя по https://cloud.redhat.com/openshift/install/azure/aro-provisioned адресу и щелкнув *скачать опрашивающий секрет*.

Вам потребуется войти в учетную запись Red Hat или создать новую учетную запись Red Hat с бизнес-почтой и принять условия.

Сохранить сохраненный `pull-secret.txt` файл в безопасном месте — он будет использоваться при каждом создании кластера.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Создание виртуальной сети, содержащей две пустые подсети

Далее вы создадите виртуальную сеть, содержащую две пустые подсети.

1. **Задайте следующие переменные.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **Создание группы ресурсов**

    Группа ресурсов Azure — это логическая группа, в которой развертываются и управляются ресурсы Azure. Во время создания группы ресурсов вам будет предложено указать расположение. В этом расположении сохраняются метаданные группы ресурсов, а также выполняется их работа в Azure, если во время создания ресурса вы не указали другой регион. Создайте группу ресурсов с помощью команды [AZ Group Create] [AZ-Group-Create].

    ```azurecli-interactive
    az group create --name $CLUSTER --location $LOCATION
    ```

    В следующем примере выходных данных показано, что группа ресурсов успешно создана:

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **Создайте виртуальную сеть.**

    Для кластеров Azure Red Hat OpenShift с OpenShift 4 требуется виртуальная сеть с двумя пустыми подсетями для главного и рабочего узлов.

    Создайте новую виртуальную сеть в той же группе ресурсов, которая была создана ранее.

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    В следующем примере выходных данных показано, что виртуальная сеть успешно создана:

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/22"
        ]
        },
        "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
        "location": "eastus",
        "name": "aro-vnet",
        "provisioningState": "Succeeded",
        "resourceGroup": "aro-rg",
        "type": "Microsoft.Network/virtualNetworks"
    }
    }
    ```

3. **Добавьте пустую подсеть для главных узлов.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **Добавьте пустую подсеть для рабочих узлов.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **[Отключите политики частной конечной точки подсети](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) в главной подсети.** Это необходимо для подключения к кластеру и управления им.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Создайте кластер.

Выполните следующую команду, чтобы создать кластер. При необходимости можно передать опрашивающий секрет, который позволяет кластеру получать доступ к реестрам контейнеров Red Hat вместе с дополнительным содержимым. Чтобы получить доступ к запросу на получение секрета, перейдите к [диспетчеру кластеров Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) и щелкните **Копировать опрашивающий секрет**.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret '$(< pull-secret.txt)' # [OPTIONAL]
```
>[!NOTE]
> Для создания кластера обычно требуется около 35 минут.

>[!IMPORTANT]
> Если вы решили указать личный домен, например **foo.example.com**, консоль OpenShift будет доступна по URL-адресу `https://console-openshift-console.apps.foo.example.com`, например, вместо встроенного домена. `https://console-openshift-console.apps.<random>.<location>.aroapp.io`
>
> По умолчанию OpenShift использует самозаверяющие сертификаты для всех маршрутов, созданных в `*.apps.<random>.<location>.aroapp.io`.  Если вы решили использовать настраиваемую службу DNS после подключения к кластеру, необходимо следовать документации по OpenShift, чтобы [настроить пользовательский ЦС для контроллера](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) входящего трафика и [Пользовательский ЦС для сервера API](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).
>

## <a name="next-steps"></a>Дальнейшие действия

В этой части руководства вы узнали, как выполнить следующие действия:
> [!div class="checklist"]
> * Настройте необходимые компоненты и создайте необходимую виртуальную сеть и подсети.
> * Развертывание кластера

Перейдите к следующему руководству:
> [!div class="nextstepaction"]
> [Подключение к кластеру OpenShift для Azure Red Hat](tutorial-connect-cluster.md)
