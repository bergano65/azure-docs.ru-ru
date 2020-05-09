---
title: Создание частного кластера Azure Red Hat OpenShift 4
description: Узнайте, как создать частный кластер Azure Red Hat OpenShift под запуском OpenShift 4.
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: ms-jasondel
ms.author: jasondel
keywords: АТО, openshift, AZ АТО, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: cfc28577f089ef22457e9f66ff08106969a5a4b2
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857392"
---
# <a name="create-an-azure-red-hat-openshift-4-private-cluster"></a>Создание частного кластера Azure Red Hat OpenShift 4

В этой статье вы подготовите среду для создания частных кластеров Azure Red Hat OpenShift с OpenShift 4. Вы научитесь:

> [!div class="checklist"]
> * Настройте необходимые компоненты и создайте необходимую виртуальную сеть и подсети.
> * Развертывание кластера с конечной точкой закрытого сервера API и закрытым контроллером входящего трафика

Если вы решили установить и использовать CLI локально, для работы с этим руководством вам потребуется Azure CLI версии 2.0.75 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Перед началом

### <a name="install-the-az-aro-extension"></a>Установите расширение "az АТО"
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

1. **[Перейдите на портал диспетчера кластеров Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) и выполните вход.**

   Вам потребуется войти в учетную запись Red Hat или создать новую учетную запись Red Hat с бизнес-почтой и принять условия.

2. **Щелкните скачать опрашивающий секрет.**

Сохранить сохраненный `pull-secret.txt` файл в безопасном месте — он будет использоваться при каждом создании кластера.

При выполнении `az aro create` команды можно ссылаться на секрет по `--pull-secret @pull-secret.txt` запросу с помощью параметра. Выполните `az aro create` из каталога, в котором сохранен `pull-secret.txt` файл. В противном `@pull-secret.txt` случае `@<path-to-my-pull-secret-file`замените на.

Если вы копируете свой опрашивающий секрет или ссылаетсяе на него в других скриптах, секретный код должен быть отформатирован в виде допустимой строки JSON.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Создание виртуальной сети, содержащей две пустые подсети

Далее вы создадите виртуальную сеть, содержащую две пустые подсети.

1. **Задайте следующие переменные.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=aro-cluster             # the name of your cluster
   ```

1. **Создание группы ресурсов**

    Группа ресурсов Azure — это логическая группа, в которой развертываются и управляются ресурсы Azure. Во время создания группы ресурсов вам будет предложено указать расположение. В этом расположении сохраняются метаданные группы ресурсов, а также выполняется их работа в Azure, если во время создания ресурса вы не указали другой регион. Создайте группу ресурсов с помощью команды [AZ Group Create] [AZ-Group-Create].

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

Выполните следующую команду, чтобы создать кластер. При необходимости можно передать запрос [на получение секрета Red Hat](#get-a-red-hat-pull-secret-optional) , который позволяет кластеру получать доступ к реестрам контейнеров Red Hat вместе с дополнительным содержимым.

>[!NOTE]
> При копировании и вставлении команд и использовании одного из необязательных параметров убедитесь, что удалены исходные хэш-Теги и текст комментария в конце. Кроме того, закройте аргумент в предыдущей строке команды с завершающей обратной косой чертой.

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

После выполнения `az aro create` команды для создания кластера обычно требуется около 35 минут.

>[!IMPORTANT]
> Если вы решили указать личный домен, например **foo.example.com**, консоль OpenShift будет доступна по URL-адресу `https://console-openshift-console.apps.foo.example.com`, например, вместо встроенного домена. `https://console-openshift-console.apps.<random>.<location>.aroapp.io`
>
> По умолчанию OpenShift использует самозаверяющие сертификаты для всех маршрутов, созданных в `*.apps.<random>.<location>.aroapp.io`.  При выборе настраиваемой службы DNS после подключения к кластеру необходимо следовать документации OpenShift, чтобы [настроить пользовательский ЦС для контроллера](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) входящих подключений и [Пользовательский ЦС для сервера API](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).

## <a name="connect-to-the-private-cluster"></a>Подключение к частному кластеру

Вы можете войти в кластер с помощью `kubeadmin` пользователя.  Выполните следующую команду, чтобы найти пароль для `kubeadmin` пользователя.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

В следующем примере выходных данных показано, что пароль будет `kubeadminPassword`находиться в.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

URL-адрес консоли кластера можно найти, выполнив следующую команду, которая будет выглядеть следующим образом:`https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

>[!IMPORTANT]
> Чтобы подключиться к частному кластеру Azure Red Hat OpenShift, необходимо выполнить следующий шаг на узле, который находится в созданной виртуальной сети, или в виртуальной сети, [которая подключена к виртуальной](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) сети, в которой был развернут кластер.

Запустите URL-адрес консоли в браузере и войдите в систему `kubeadmin` , используя учетные данные.

![Экран входа в Azure Red Hat OpenShift](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>Установка интерфейса командной строки OpenShift

Войдя в веб-консоль OpenShift, щелкните **?** в правом верхнем углу, а затем в **средствах командной строки**. Скачайте выпуск, соответствующий вашему компьютеру.

![Экран входа в Azure Red Hat OpenShift](media/aro4-download-cli.png)

Вы также можете скачать последнюю версию интерфейса командной строки, подходящую для компьютера, <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/>с.

## <a name="connect-using-the-openshift-cli"></a>Подключение с помощью интерфейса командной строки OpenShift

Получение адреса сервера API.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

>[!IMPORTANT]
> Чтобы подключиться к частному кластеру Azure Red Hat OpenShift, необходимо выполнить следующий шаг на узле, который находится в созданной виртуальной сети, или в виртуальной сети, [которая подключена к виртуальной](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) сети, в которой был развернут кластер.

Войдите на сервер API кластера OpenShift с помощью следующей команды. Замените ** \<кубеадмин Password>** паролем, который вы только что получили.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Дальнейшие шаги

В этой статье был развернут кластер Azure Red Hat OpenShift с запущенным OpenShift 4. Вы ознакомились с выполнением следующих задач:

> [!div class="checklist"]
> * Настройте необходимые компоненты и создайте необходимую виртуальную сеть и подсети.
> * Развертывание кластера
> * Подключение к кластеру с помощью `kubeadmin` пользователя

Перейдите к следующей статье, чтобы узнать, как настроить кластер для проверки подлинности с помощью Azure Active Directory.


* [Настройка проверки подлинности с помощью Azure Active Directory командной строки](configure-azure-ad-cli.md)


* [Настройка проверки подлинности с помощью Azure Active Directory в веб-консоли портал Azure и OpenShift](configure-azure-ad-cli.md)
