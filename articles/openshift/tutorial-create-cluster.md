---
title: Учебник по созданию кластера Azure Red Hat OpenShift 4
description: Узнайте, как создать кластер Microsoft Azure Red Hat OpenShift с помощью Azure CLI.
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 9a393e29c4b5b2faa48cbcd273c7bc7a46169ba3
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904196"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Руководство по созданию кластера Azure Red Hat OpenShift 4

В этом учебнике (часть 1 из 3) вы подготовите среду для создания кластера Azure Red Hat OpenShift с OpenShift 4 и создадите кластер. Вы научитесь:
> [!div class="checklist"]
> * настраивать необходимые компоненты и создавать необходимую виртуальную сеть и подсети;
> * Развертывание кластера

## <a name="before-you-begin"></a>Перед началом

Если вы решили установить и использовать интерфейс командной строки локально, то для работы с этим руководством вам понадобится Azure CLI 2.0.75 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="verify-your-permissions"></a>Проверка разрешений

Чтобы создать кластер Azure Red Hat OpenShift, проверьте следующие разрешения для подписки Azure, пользователя Azure Active Directory и субъекта-службы.

|Разрешения|Группа ресурсов, которая содержит виртуальную сеть|Пользователь, выполняющий `az aro create`|Субъект-служба, передаваемый как `–client-id`|
|----|:----:|:----:|:----:|
|**Администратор доступа пользователей**|X|X| |
|**Участник**|X|X|X|

### <a name="register-the-resource-provider"></a>Регистрация поставщика ресурсов

Затем необходимо зарегистрировать поставщик ресурсов `Microsoft.RedHatOpenShift` в подписке.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Получение секрета для извлечения Red Hat (необязательно)

Секрет для извлечения Red Hat позволяет вашему кластеру получать доступ к реестрам контейнера Red Hat и дополнительному содержимому. Этот шаг необязателен, но мы рекомендуем его выполнить.

1. **[Перейдите на портал диспетчера кластеров Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) и выполните вход.**

   Вам потребуется войти в учетную запись Red Hat или создать учетную запись Red Hat с помощью рабочего адреса электронной почты и принять условия.

2. Перейдите на страницу [**продукта OpenShift**](https://developers.redhat.com/products/codeready-containers), если вы создаете кластер впервые. После регистрации перейдите на [**страницу диспетчера кластеров Red Hat OpenShift**](https://cloud.redhat.com/openshift/) и щелкните **Download pull secret** (Скачать секрет для извлечения), чтобы скачать секрет для использования с кластером АRO.

Сохраните файл `pull-secret.txt` в безопасном месте. Этот файл будет использоваться при каждом создании кластера, если вам потребуется создать кластер, включающий примеры или операторы для партнеров Red Hat или сертифицированных партнеров.

При выполнении команды `az aro create` вы можете ссылаться на секрет для извлечения, используя параметр `--pull-secret @pull-secret.txt`. Выполните `az aro create` из каталога, в котором был сохранен файл `pull-secret.txt`. В противном случае измените `@pull-secret.txt` на `@<path-to-my-pull-secret-file>`.

Если вы копируете свой секрет для извлечения или ссылаетесь на него в других скриптах, он должен быть отформатирован в виде допустимой строки JSON.

### <a name="prepare-a-custom-domain-for-your-cluster-optional"></a>Подготовка личного домена для кластера (дополнительно)

При выполнении команды `az aro create` можно указать личный домен для кластера с использованием параметра `--domain foo.example.com`.

Если вы предоставляете личный домен для кластера, учитывайте следующие моменты:

* После создания кластера необходимо создать две записи DNS типа A на DNS-сервере для указанного значения`--domain`:
    * запись **api**, указывающую на сервер API;
    * запись **\*.apps**, указывающую на входящий трафик.
    * Извлеките эти значения, выполнив следующую команду: `az aro show -n -g --query '{api:apiserverProfile.ip, ingress:ingressProfiles[0].ip}'`.

* Консоль OpenShift будет доступна по URL-адресу, такому как `https://console-openshift-console.apps.foo.example.com`, вместо встроенного домена `https://console-openshift-console.apps.<random>.<location>.aroapp.io`.

* По умолчанию OpenShift использует самозаверяющие сертификаты для всех маршрутов, созданных в `*.apps.<random>.<location>.aroapp.io`.  Если вы решили использовать пользовательскую службу DNS после подключения к кластеру, необходимо выполнить действия, описанные в документации по OpenShift, чтобы [настроить пользовательский ЦС для контроллера входящего трафика](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) и [пользовательский ЦС для сервера API](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Создание виртуальной сети, содержащей две пустые подсети

Далее вы создадите виртуальную сеть, содержащую две пустые подсети.

1. **Задайте указанные ниже переменные в среде оболочки, где будут выполняться команды `az`.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **Создайте группу ресурсов.**

    Группа ресурсов Azure — это логическая группа, в которой развертываются и управляются ресурсы Azure. Во время создания группы ресурсов вам будет предложено указать расположение. В этом расположении сохраняются метаданные группы ресурсов, а также выполняется их работа в Azure, если во время создания ресурса вы не указали другой регион. Создайте группу ресурсов с помощью команды [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create).

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

    Создайте виртуальную сеть в той же группе ресурсов, которая была создана ранее:

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

Чтобы создать кластер, выполните команду ниже. Если вы решили использовать один из указанных ниже параметров, измените команду соответствующим образом:
* При необходимости вы можете [передать секрет для извлечения Red Hat](#get-a-red-hat-pull-secret-optional), который позволяет кластеру получать доступ к реестрам контейнеров Red Hat, а также к дополнительному содержимому. Добавьте в команду аргумент `--pull-secret @pull-secret.txt`.
* При необходимости можно [использовать личный домен](#prepare-a-custom-domain-for-your-cluster-optional). Добавьте в команду аргумент `--domain foo.example.com`, заменив `foo.example.com` собственным личным доменом.

> [!NOTE]
> Если вы добавляете в команду необязательные аргументы, обязательно закройте аргумент в предыдущей строке команды конечной обратной косой чертой.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
```

После выполнения команды `az aro create` создание кластера обычно занимает около 35 минут.

## <a name="next-steps"></a>Дальнейшие действия

В этой части руководства вы узнали, как выполнить следующие действия:
> [!div class="checklist"]
> * настраивать необходимые компоненты, а также создавать требуемую виртуальную сеть и подсети;
> * Развертывание кластера

Перейдите к следующему руководству:
> [!div class="nextstepaction"]
> [Подключение к кластеру Azure Red Hat OpenShift](tutorial-connect-cluster.md)
