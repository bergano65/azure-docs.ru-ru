---
title: Создание кластера Azure Red Hat OpenShift 4,3 | Документация Майкрософт
description: Создание кластера с помощью Azure Red Hat OpenShift 4,3
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: АТО, openshift, AZ АТО, Red Hat, CLI
ms.openlocfilehash: f909c5870be6e394e457ad8f44ea5a253054ffe6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81398893"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Создание кластера Azure Red Hat OpenShift 4,3, доступ к нему и управление им

> [!IMPORTANT]
> Обратите внимание, что Azure Red Hat OpenShift 4,3 сейчас доступна только в закрытой предварительной версии в восточной части США и восточной части США 2. Закрытая Предварительная версия принимает только приглашение. Перед включением этой функции обязательно Зарегистрируйте подписку: [Регистрация закрытой предварительной версии Azure Red Hat OpenShift](https://aka.ms/aro-preview-register)

> [!NOTE]
> Функции предварительной версии являются самостоятельными и предоставляются как есть и доступны и исключаются из соглашения об уровне обслуживания (SLA) и ограниченной гарантии. Поэтому функции не предназначены для использования в рабочей среде.

## <a name="prerequisites"></a>Предварительные условия

Для создания кластера Azure Red Hat OpenShift 4,3 вам потребуется следующее:

- Azure CLI версии 2.0.72 или выше
  
- Расширение "az АТО"

- Виртуальная сеть, содержащая две пустые подсети, каждая из которых не подключена к группе безопасности сети.  Кластер будет развернут в этих подсетях.

- Приложение AAD кластера (идентификатор клиента и секрет) и субъект-служба или достаточные разрешения AAD для `az aro create` автоматического создания приложения AAD и субъекта-службы.

- Субъект-служба RP и субъект-служба кластера должны иметь роль участника в виртуальной сети кластера.  Если у вас есть роль "администратор доступа пользователей" в виртуальной сети, `az aro create` автоматически настроит назначения ролей.

### <a name="install-the-az-aro-extension"></a>Установите расширение "az АТО"
`az aro` Расширение позволяет создавать, получать доступ и удалять кластеры OpenShift для Azure Red Hat непосредственно из командной строки с помощью Azure CLI.

> [!Note] 
> `az aro` Расширение является актуальным в предварительной версии. Он может быть изменен или удален в будущем выпуске.
> Чтобы принять участие в предварительной `az aro` версии расширения, необходимо зарегистрировать поставщик `Microsoft.RedHatOpenShift` ресурсов.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Войдите в Azure.

   ```console
   az login
   ```

2. Выполните следующую команду, чтобы установить `az aro` расширение:

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. Убедитесь, что расширение АТО зарегистрировано.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.3.0
   ...
   ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Получение секрета по запросу Red Hat (необязательно)

Секретный запрос Red Hat позволяет вашему кластеру получать доступ к реестрам контейнеров Red Hat и дополнительному содержимому. Использование секрета для получения по запросу является необязательным, но рекомендуется.

Чтобы получить секрет по запросу:

1. Перейдите в расположение https://cloud.redhat.com/openshift/install/azure/aro-provisioned.
1. Войдите в свою учетную запись Red Hat или создайте новую учетную запись Red Hat с помощью своей бизнес-электронной почты. Примите условия.
1. Выберите **скачать секрет для опрашивающей репликации**.

Сохраните файл *пулл-секрет. txt* в безопасном месте; Этот файл будет использоваться каждый раз при создании кластера.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Создание виртуальной сети, содержащей две пустые подсети

Выполните следующие действия, чтобы создать виртуальную сеть, содержащую две пустые подсети.

1. Задайте следующие переменные.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   ```

2. Создайте группу ресурсов для кластера.

   ```console
   az group create -g "$RESOURCEGROUP" -l $LOCATION
   ```

3. Создание виртуальной сети.

   ```console
   az network vnet create \
     -g "$RESOURCEGROUP" \
     -n vnet \
     --address-prefixes 10.0.0.0/9 \
     >/dev/null
   ```

4. Добавьте две пустые подсети в виртуальную сеть.

   ```console
   for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
     az network vnet subnet create \
       -g "$RESOURCEGROUP" \
       --vnet-name vnet \
       -n "$subnet" \
       --address-prefixes 10.$((RANDOM & 127)).$((RANDOM & 255)).0/24 \
       --service-endpoints Microsoft.ContainerRegistry \
       >/dev/null
   done
   ```

5. Отключите сетевые политики для службы частной связи в виртуальной сети и подсетях. Это требование для службы АТО для доступа к кластеру и управления им.

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>Создание кластера

Выполните следующую команду, чтобы создать кластер.

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --cluster-resource-group "aro-$CLUSTER" \
  --domain "$CLUSTER" \
  --pull-secret @pull-secret.txt
```

>[!NOTE]
> Для создания кластера обычно требуется около 35 минут.

## <a name="access-the-cluster-console"></a>Доступ к консоли кластера

URL-адрес консоли кластера (в формате `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`) можно найти в ресурсе кластера Azure Red Hat OpenShift 4,3. Выполните следующую команду, чтобы просмотреть ресурс:

```console
az aro list -o table
```

Вы можете войти в кластер с помощью `kubeadmin` пользователя.  Выполните следующую команду, чтобы найти пароль для `kubeadmin` пользователя:

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>Удаление кластера

Выполните следующую команду, чтобы удалить кластер.

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```
