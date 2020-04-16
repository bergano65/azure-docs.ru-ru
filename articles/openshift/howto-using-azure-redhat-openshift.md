---
title: Создайте кластер Azure Red Hat OpenShift 4.3 Документы Майкрософт
description: Создайте кластер с Azure Red Hat OpenShift 4.3
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: аро, openshift, аз-аро, красная шляпа, кли
ms.openlocfilehash: f909c5870be6e394e457ad8f44ea5a253054ffe6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398893"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Создание, доступ и управление кластером Azure Red Hat OpenShift 4.3

> [!IMPORTANT]
> Обратите внимание, что Azure Red Hat OpenShift 4.3 в настоящее время доступна только в частном предварительном просмотре в Восточной США и Восточной США 2. Частное предварительное принятие только по приглашению. Пожалуйста, не забудьте зарегистрировать подписку, прежде чем пытаться включить эту функцию: [Azure Red Hat OpenShift Частная регистрация предварительного просмотра](https://aka.ms/aro-preview-register)

> [!NOTE]
> Функции предварительного просмотра являются самообслуживанием и предоставляются как есть и по мере возможности и исключены из соглашения об уровне обслуживания (SLA) и ограниченной гарантии. Таким образом, функции не предназначены для использования в производстве.

## <a name="prerequisites"></a>Предварительные требования

Для создания кластера Azure Red Hat OpenShift 4.3 необходимо создать кластер Azure Red Hat OpenShift 4.3:

- Версия Azure CLI 2.0.72 или больше
  
- Расширение 'az aro'

- Виртуальная сеть, содержащая две пустые подсети, каждая из которых не привязана к группе сетевой безопасности.  Ваш кластер будет развернут в этих подсете.

- Кластерное приложение AAD (идентификатор клиента и секрет) `az aro create` и основной сервис, или достаточное количество разрешений AAD для создания приложения AAD и принципа обслуживания для вас автоматически.

- Директор службы RP и директор кластерного обслуживания должны иметь роль вкладчика в виртуальной сети кластера.  Если у вас есть роль "Администратор пользовательского `az aro create` доступа" в виртуальной сети, настроить назначения ролей для вас автоматически.

### <a name="install-the-az-aro-extension"></a>Установка расширения 'az aro'
Расширение `az aro` позволяет создавать, получать доступ и удалять кластеры Azure Red Hat OpenShift непосредственно из командной строки с помощью Azure CLI.

> [!Note] 
> Расширение `az aro` является currenty в предварительном просмотре. Он может быть изменен или удален в будущем выпуске.
> Для того, чтобы `az aro` зарегистрироваться для предварительного просмотра расширения, необходимо зарегистрировать поставщика `Microsoft.RedHatOpenShift` ресурсов.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Войдите в Azure.

   ```console
   az login
   ```

2. Выполнить следующую команду `az aro` для установки расширения:

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. Проверка расширения ARO зарегистрирована.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.3.0
   ...
   ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Получить Секрет притяжения Red Hat (по желанию)

Секрет притяжения Red Hat позволяет вашему кластеру получить доступ к реестрам контейнеров Red Hat и дополнительному контенту. Использование тайны тянуть является необязательным, но рекомендуется.

Чтобы получить ваш тянуть секрет:

1. Перейдите к https://cloud.redhat.com/openshift/install/azure/aro-provisioned.
1. Войдите в учетную запись Red Hat или создайте новую учетную запись Red Hat, используя свою деловую электронную почту; принять условия.
1. Выберите **Скачать тянуть секрет**.

Сохранить *файл pull-secret.txt где-нибудь* безопасно; вы будете использовать файл каждый раз, когда вы создаете кластер.

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

5. Отменяйте сетевые политики для службы private Link в виртуальной сети и подсетях. Это требование к службе ARO для доступа к кластеру и управления им.

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>Создание кластера

Выполнить следующую команду для создания кластера.

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
> Обычно создание кластера занимает около 35 минут.

## <a name="access-the-cluster-console"></a>Доступ к кластерной консоли

URL-адрес кластерной консоли (формы) `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`можно найти под ресурсом кластера Azure Red Hat OpenShift 4.3. Выполнить следующую команду для просмотра ресурса:

```console
az aro list -o table
```

Вы можете войти в `kubeadmin` кластер с помощью пользователя.  Выполнить следующую команду, чтобы `kubeadmin` найти пароль для пользователя:

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>Удаление кластера

Выполнить следующую команду, чтобы удалить кластер.

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```
