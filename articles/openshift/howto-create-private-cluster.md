---
title: Создайте частный кластер с Azure Red Hat OpenShift 3.11 Документы Майкрософт
description: Создание частного кластера с Azure Red Hat OpenShift 3.11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: аро, openshift, частный кластер, красная шляпа
ms.openlocfilehash: b34b5d622527742447847102526eba9ee6ca220d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399423"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Создание частного кластера с Azure Red Hat OpenShift 3.11

> [!IMPORTANT]
> Частные кластеры Azure Red Hat OpenShift (ARO) в настоящее время доступны только в приватном предварительном просмотре в East US 2. Частное предварительное принятие только по приглашению. Пожалуйста, не забудьте зарегистрировать подписку, прежде чем пытаться включить эту функцию.

Частные кластеры предоставляют следующие преимущества:

* Частные кластеры не разоблачают компоненты плоскости управления кластерами (например, серверы API) на общедоступном IP-адресе.
* Виртуальная сеть частного кластера настраивается клиентами, что позволяет настроить сеть, позволяющую вглядываться в другие виртуальные сети, включая среды ExpressRoute. Вы также можете настроить пользовательские DNS в виртуальной сети для интеграции с внутренними службами.

## <a name="before-you-begin"></a>Перед началом

> [!NOTE]
> Эта функция требует версии 2019-10-27-превью ARO HTTP API. Он еще не поддерживается в Azure CLI.

Поля в следующем фрагменте конфигурации являются новыми и должны быть включены в конфигурацию кластера. `managementSubnetCidr`должна находиться в виртуальной сети кластера и использоваться Azure для управления кластером.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Частный кластер может быть развернут с помощью примеров скриптов, приведенных ниже. После развертывания кластера выполните `cluster get` команду и `properties.FQDN` просмотрите свойство для определения частного IP-адреса сервера OpenShift API.

Кластерная виртуальная сеть будет создана с разрешениями, чтобы вы могли изменить ее. Затем вы можете настроить сеть для доступа к виртуальной сети (ExpressRoute, VPN, виртуальная сеть пиринга), как это требуется для ваших нужд.

Если вы измените DNS-серверы имен в виртуальной сети кластера, то `properties.RefreshCluster` вам `true` нужно будет выпустить обновление кластера с набором свойств, чтобы можно было переоформить виртуальные выдвиженцы. Это обновление позволит им подобрать новые серверы имен.

## <a name="sample-configuration-scripts"></a>Примеры сценариев конфигурации

Используйте примеры скриптов в этом разделе для настройки и развертывания частного кластера.

### <a name="environment"></a>Среда

Заполните переменные среды ниже, используя свои собственные значения.

> [!NOTE]
> Местоположение должно быть `eastus2` установлено, поскольку в настоящее время это единственное поддерживаемое место для частных кластеров.

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

### <a name="private-clusterjson"></a>private-cluster.json

Используя переменные среды, определенные выше, вот пример кластера конфигурации с частным кластером включен.

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

После настройки частного кластера с приведенными выше примерами скриптов запустите следующую команду для развертывания частного кластера.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать о том, как получить доступ к консоли OpenShift, смотрите [веб-консоль Walkthrough](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html).
