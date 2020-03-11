---
title: Создание кластера Azure Red Hat OpenShift 4,3 | Документация Майкрософт
description: Создание кластера с помощью Azure Red Hat OpenShift 3,11
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: АТО, openshift, AZ АТО, Red Hat, CLI
ms.openlocfilehash: 3c336a1fbfb9f991ff824e8deafe84f3d899771d
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082835"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Создание кластера Azure Red Hat OpenShift 4,3, доступ к нему и управление им

> [!IMPORTANT]
> Обратите внимание, что Azure Red Hat OpenShift 4,3 в настоящее время доступен только в закрытой предварительной версии в восточной части США. Закрытая Предварительная версия принимает только приглашение. Перед включением этой функции обязательно Зарегистрируйте подписку: [Регистрация закрытой предварительной версии Azure Red Hat OpenShift](https://aka.ms/aro-preview-register)

> [!NOTE]
> Функции предварительной версии являются самостоятельными и предоставляются как есть и доступны и исключаются из соглашения об уровне обслуживания (SLA) и ограниченной гарантии. Поэтому функции не предназначены для использования в рабочей среде.

## <a name="prerequisites"></a>предварительные требования

Для создания кластера Azure Red Hat OpenShift 4,3 вам потребуется следующее:

- Azure CLI версии 2.0.72 или выше
  
- Расширение "az АТО"

- Виртуальная сеть, содержащая две пустые подсети, каждая из которых не подключена к группе безопасности сети.  Кластер будет развернут в этих подсетях.

- Приложение AAD кластера (идентификатор клиента и секрет) и субъект-служба или достаточные разрешения AAD для `az aro create` для автоматического создания приложения AAD и субъекта-службы.

- Субъект-служба RP и субъект-служба кластера должны иметь роль участника в виртуальной сети кластера.  Если у вас есть роль "администратор доступа пользователей" в виртуальной сети, `az aro create` настроит назначения ролей автоматически.

### <a name="install-the-az-aro-extension"></a>Установите расширение "az АТО"
Расширение `az aro` позволяет создавать, получать доступ к кластерам Azure Red Hat OpenShift и удалять их непосредственно из командной строки с помощью Azure CLI.

> [!Note] 
> Расширение `az aro` является актуальным в предварительной версии. Он может быть изменен или удален в будущем выпуске.
> Чтобы принять участие в предварительной версии расширения `az aro`, необходимо зарегистрировать поставщик ресурсов `Microsoft.RedHatOpenShift`.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Войдите в Azure.

   ```console
   az login
   ```

2. Выполните следующую команду, чтобы установить расширение `az aro`:

   ```console
   az extension add --source https://arosvc.blob.core.windows.net/az-preview/aro-0.1.0-py2.py3-none-any.whl
   ```

3. Убедитесь, что расширение АТО зарегистрировано.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.1.0
   ...
   ```
  
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
  --worker-subnet "$CLUSTER-worker"
```

>[!NOTE]
> Для создания кластера обычно требуется около 35 минут.

## <a name="access-the-cluster-console"></a>Доступ к консоли кластера

URL-адрес консоли кластера (в формате `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`) можно найти в ресурсе кластера Azure Red Hat OpenShift 4,3. Выполните следующую команду, чтобы просмотреть ресурс:

```console
az aro list -o table
```

Вы можете войти в кластер с помощью `kubeadmin` пользователя.  Выполните следующую команду, чтобы найти пароль для пользователя `kubeadmin`:

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