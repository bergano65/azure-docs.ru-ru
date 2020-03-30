---
title: Создание сред интеграции служб (ISE) с помощью API logic Apps REST
description: Создайте среду интеграционных служб (ISE) с помощью Logic Apps REST API, чтобы вы могли получить доступ к виртуальным сетям Azure (VNETs) из приложений Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 2c6e35b1e7d160064998004f87c5b14d0eaeac5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127656"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Создание среды интеграционных служб (ISE) с помощью Logic Apps REST API

В этой статье показано, как создать [ *среду интеграционных служб* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) через Logic Apps REST API для сценариев, где ваши логические приложения и учетные записи интеграции нуждаются в доступе к [виртуальной сети Azure.](../virtual-network/virtual-networks-overview.md) ISE — это изолированная среда, использующая специализированное хранилище и другие ресурсы, которые хранятся отдельно от «глобального» мультитенантного сервиса Logic Apps. Такое разделение помогает уменьшить любое влияние других клиентов Azure на производительность вашего приложения. ISE также предоставляет вам свои собственные статические IP-адреса. Эти IP-адреса отделены от статических IP-адресов, которые используются логическими приложениями в общедоступной мультитенантной службе.

Вместо этого с помощью портала Azure можно [прослушать виртуальные сети Connect to Azure из приложений Azure Logic Apps.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

> [!IMPORTANT]
> Логические приложения, встроенные триггеры, встроенные действия и разъемы, которые работают в ISE, используют план ценообразования, отличный от плана ценообразования на основе потребления. Чтобы узнать, как ценообразование и выставление счетов для ISEs, [см.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Для ценообразования цены, см [Логика Apps ценообразования](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Предварительные требования

* Те же [предпосылки](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) и [требования для обеспечения доступа к ВАШЕМу ISE,](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) как при создании ISE на портале Azure

* Инструмент, который можно использовать для создания ISE, позвонив в API Logic Apps REST с запросом HTTPS PUT. Например, можно использовать [Postman](https://www.getpostman.com/downloads/)или создать логическое приложение, выполняя задачу.

## <a name="send-the-request"></a>Отправка запроса

Чтобы создать ISE, позвонив в Logic Apps REST API, сделайте этот запрос HTTPS PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Логика Apps REST API 2019-05-01 версия требует, чтобы вы сделали свой собственный запрос HTTP PUT для разъемов ISE.

Развертывание обычно занимает в течение двух часов. Иногда развертывание может занять до четырех часов. Чтобы проверить состояние развертывания, на [портале Azure](https://portal.azure.com)на панели инструментов Azure выберите значок уведомлений, который открывает панель уведомлений.

> [!NOTE]
> Если развертывание завершится ошибкой или вы удалите среду службы интеграции, освобождение подсетей в Azure может занять до одного часа. Эта задержка означает, что вам, возможно, придется подождать, прежде чем повторно использовать эти подсети в другом ISE.
>
> Если вы удалите виртуальную сеть, Azure обычно занимает до двух часов, прежде чем выпустить ваши подсети, но эта операция может занять больше времени. 
> При удалянии виртуальных сетей убедитесь, что ресурсы по-прежнему не подключены. 
> Смотрите [Удалить виртуальную сеть](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Заголовок запроса

В заголовке запроса включите следующие свойства:

* `Content-type`: Установите это `application/json`значение свойства для .

* `Authorization`: Установите это значение свойства маркеру носителя для клиента, который имеет доступ к подписной или ресурсной группе Azure, которую вы хотите использовать.

### <a name="request-body-syntax"></a>Синтаксис текста запроса

Вот синтаксис тела запроса, который описывает свойства, которые можно использовать при создании ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      }
   }
}
```

### <a name="request-body-example"></a>Пример тела запроса

В этом корпусе запроса пример аудируется значения выборки:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      }
   }
}
```

## <a name="next-steps"></a>Дальнейшие действия

* [Добавление ресурсов в среды служб интеграции](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Управление средами службы интеграции](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

