---
title: Создание частного кластера с помощью Azure Red Hat OpenShift 3,11 | Документация Майкрософт
description: Создание частного кластера с помощью Azure Red Hat OpenShift 3,11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: АТО, openshift, частный кластер, Red Hat
ms.openlocfilehash: b34b5d622527742447847102526eba9ee6ca220d
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399423"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Создание частного кластера с помощью Azure Red Hat OpenShift 3,11

> [!IMPORTANT]
> Частные кластеры Azure Red Hat OpenShift (АТО) в настоящее время доступны только в закрытой предварительной версии в восточной части США 2. Закрытая Предварительная версия принимает только приглашение. Обязательно зарегистрируйте подписку, прежде чем пытаться включить эту функцию.

Частные кластеры предоставляют следующие преимущества.

* Частные кластеры не предоставляют компоненты плоскости управления кластером (такие как серверы API) на общедоступном IP-адресе.
* Виртуальная сеть частного кластера настраивается клиентами, что позволяет настроить сеть для поддержки пиринга с другими виртуальными сетями, включая среды ExpressRoute. Вы также можете настроить пользовательский DNS в виртуальной сети для интеграции с внутренними службами.

## <a name="before-you-begin"></a>Перед началом

> [!NOTE]
> Для работы этой функции требуется версия 2019-10-27 API HTTP АТО-Preview. Он еще не поддерживается в Azure CLI.

Поля в следующем фрагменте конфигурации являются новыми и должны быть включены в конфигурацию кластера. `managementSubnetCidr` должны находиться в виртуальной сети кластера и использоваться Azure для управления кластером.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Частный кластер можно развернуть с помощью примеров сценариев, приведенных ниже. После развертывания кластера выполните команду `cluster get` и просмотрите свойство `properties.FQDN`, чтобы определить частный IP-адрес сервера API OpenShift.

Виртуальная сеть кластера будет создана с разрешениями, чтобы ее можно было изменить. После этого можно настроить сеть для доступа к виртуальной сети (ExpressRoute, VPN, пиринг виртуальной сети) в соответствии с вашими потребностями.

Если изменить DNS-серверах имен в виртуальной сети кластера, необходимо будет выполнить обновление в кластере со свойством `properties.RefreshCluster`, имеющим значение `true`, чтобы можно было повторно создать образ виртуальных машин. Это обновление позволит им выбрать новый серверах имен.

## <a name="sample-configuration-scripts"></a>Примеры сценариев конфигурации

Используйте примеры сценариев в этом разделе, чтобы настроить и развернуть частный кластер.

### <a name="environment"></a>Среда

Заполните переменные среды ниже, используя собственные значения.

> [!NOTE]
> Расположение должно быть установлено в `eastus2` так как в настоящее время это единственное поддерживаемое расположение для частных кластеров.

``` bash
export CLUSTER_NAME=
export LOCATION=eastus2
export TOKEN=$(az account get-access-token --query 'accessToken' -o tsv)
export SUBID=
export TENANT_ID=
export ADMIN_GROUP=
export CLIENT_ID=
export SECRET=
```

### <a name="private-clusterjson"></a>частный — Cluster. JSON

Ниже приведен пример конфигурации кластера с включенным частным кластером с использованием переменных среды, определенных выше.

```json
{
   "location": "$LOCATION",
   "name": "$CLUSTER_NAME",
   "properties": {
       "openShiftVersion": "v3.11",
       "networkProfile": {
           "vnetCIDR": "10.0.0.0/8",
           "managementSubnetCIDR" : "10.0.1.0/24"
       },
       "authProfile": {
           "identityProviders": [
               {
                   "name": "Azure AD",
                   "provider": {
                       "kind": "AADIdentityProvider",
                       "clientId": "$CLIENT_ID",
                       "secret": "$SECRET",
                       "tenantId": "$TENANT_ID",
                       "customerAdminGroupID": "$ADMIN_GROUP"
                   }
               }
           ]
       },
       "masterPoolProfile": {
           "name": "master",
           "count": 3,
           "vmSize": "Standard_D4s_v3",
           "osType": "Linux",
           "subnetCIDR": "10.0.0.0/24",
           "apiProperties": {
                "privateApiServer": true
            }
       },
       "agentPoolProfiles": [
           {
               "role": "compute",
               "name": "compute",
               "count": 1,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           },
           {
               "role": "infra",
               "name": "infra",
               "count": 3,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           }
       ],
       "routerProfiles": [
           {
               "name": "default"
           }
       ]
   }
}
```

## <a name="deploy-a-private-cluster"></a>Развертывание частного кластера

После настройки частного кластера с помощью приведенных выше примеров сценариев выполните следующую команду, чтобы развернуть частный кластер.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о доступе к консоли OpenShift см. в разделе [Пошаговое руководство по веб-консоли](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html).
