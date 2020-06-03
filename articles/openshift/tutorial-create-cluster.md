---
title: Учебник по созданию кластера Azure Red Hat OpenShift 4
description: Узнайте, как создать кластер Microsoft Azure Red Hat OpenShift с помощью Azure CLI.
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: f8b34f1678d39471a1d0b91756ac93a01cbfedba
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800165"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Руководство по созданию кластера Azure Red Hat OpenShift 4

В этом учебнике (часть 1 из 3) вы подготовите среду для создания кластера Azure Red Hat OpenShift с OpenShift 4 и создадите кластер. Вы научитесь:
> [!div class="checklist"]
> * настраивать необходимые компоненты и создавать необходимую виртуальную сеть и подсети;
> * Развертывание кластера

## <a name="before-you-begin"></a>Перед началом

Если вы решили установить и использовать интерфейс командной строки локально, то для работы с этим руководством вам понадобится Azure CLI 2.0.75 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="verify-your-permissions"></a>Проверка разрешений

Чтобы создать кластер Azure Red Hat OpenShift, проверьте следующие разрешения для учетной записи Azure и пользователя.

|Разрешения|Группа ресурсов, которая содержит виртуальную сеть|Пользователь, выполняющий `az aro create`|Субъект-служба, передаваемый как `–client-id`|
|----|:----:|:----:|:----:|
|**Администратор доступа пользователей**|X|X| |
|**Участник**|X|X|X|

### <a name="install-the-az-aro-extension"></a>Установка расширения `az aro`
Расширение `az aro` позволяет создавать и удалять кластеры Azure Red Hat OpenShift и получать к ним доступ непосредственно из командной строки с помощью Azure CLI.

Чтобы установить расширение `az aro`, выполните следующую команду:

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

Если расширение уже установлено, его можно обновить, выполнив следующую команду:

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>Регистрация поставщика ресурсов

Затем необходимо зарегистрировать поставщик ресурсов `Microsoft.RedHatOpenShift` в подписке.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

Убедитесь, что расширение зарегистрировано.

```azurecli-interactive
az -v
```

  Должен отобразиться результат, аналогичный приведенному ниже.

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Получение секрета для извлечения Red Hat (необязательно)

Секрет для извлечения Red Hat позволяет вашему кластеру получать доступ к реестрам контейнера Red Hat и дополнительному содержимому. Этот шаг необязателен, но мы рекомендуем его выполнить.

1. **[Перейдите на портал диспетчера кластеров Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) и выполните вход.**

   Вам потребуется войти в учетную запись Red Hat или создать учетную запись Red Hat с помощью рабочего адреса электронной почты и принять условия.

2. **Щелкните Download pull secret (Скачать секрет для извлечения).**

Сохраненный файл `pull-secret.txt` должен находиться в безопасном месте — он будет использоваться при каждом создании кластера.

При выполнении команды `az aro create` вы можете ссылаться на секрет для извлечения, используя параметр `--pull-secret @pull-secret.txt`. Выполните `az aro create` из каталога, в котором был сохранен файл `pull-secret.txt`. В противном случае измените `@pull-secret.txt` на `@<path-to-my-pull-secret-file>`.

Если вы копируете свой секрет для извлечения или ссылаетесь на него в других скриптах, он должен быть отформатирован в виде допустимой строки JSON.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Создание виртуальной сети, содержащей две пустые подсети

Далее вы создадите виртуальную сеть, содержащую две пустые подсети.

1. **Задайте следующие переменные.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **Создайте группу ресурсов**.

    Группа ресурсов Azure — это логическая группа, в которой развертываются и управляются ресурсы Azure. Во время создания группы ресурсов вам будет предложено указать расположение. В этом расположении сохраняются метаданные группы ресурсов, а также выполняется их работа в Azure, если во время создания ресурса вы не указали другой регион. Создайте группу ресурсов с помощью команды [az group create][az-group-create].

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
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

    Для кластеров Azure Red Hat OpenShift с OpenShift 4 требуется виртуальная сеть с двумя пустыми подсетями для главного и рабочего узлов.

    Создайте виртуальную сеть в той же группе ресурсов, которая была создана ранее.

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

Чтобы создать кластер, выполните команду ниже. При необходимости вы можете [передать секрет для извлечения Red Hat](#get-a-red-hat-pull-secret-optional), который позволяет кластеру получать доступ к реестрам контейнеров Red Hat, а также к дополнительному содержимому.

>[!NOTE]
> При копировании и вставке команд, а также использовании одного из необязательных параметров убедитесь, что исходные хэш-теги и комментарии в конце удалены. Кроме того, добавьте к аргументу в предыдущей строке команды обратную косую черту в конце.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

После выполнения команды `az aro create` создание кластера обычно занимает около 35 минут.

>[!IMPORTANT]
> Если вы решили указать личный домен, например **foo.example.com**, консоль OpenShift будет доступна по URL-адресу, например `https://console-openshift-console.apps.foo.example.com`, вместо встроенного адреса `https://console-openshift-console.apps.<random>.<location>.aroapp.io` домена.
>
> По умолчанию OpenShift использует самозаверяющие сертификаты для всех маршрутов, созданных в `*.apps.<random>.<location>.aroapp.io`.  Если вы решили использовать пользовательскую службу DNS после подключения к кластеру, необходимо выполнить действия, описанные в документации по OpenShift, чтобы [настроить пользовательский ЦС для контроллера входящего трафика](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) и [пользовательский ЦС для сервера API](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).
>

## <a name="next-steps"></a>Дальнейшие действия

В этой части руководства вы узнали, как выполнить следующие действия:
> [!div class="checklist"]
> * настраивать необходимые компоненты и создавать необходимую виртуальную сеть и подсети;
> * Развертывание кластера

Перейдите к следующему руководству:
> [!div class="nextstepaction"]
> [Подключение к кластеру Azure Red Hat OpenShift](tutorial-connect-cluster.md)
