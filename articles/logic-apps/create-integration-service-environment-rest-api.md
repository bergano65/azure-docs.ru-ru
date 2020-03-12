---
title: Создание сред службы интеграции (Исес) с помощью Logic Apps REST API
description: Создайте среду службы интеграции (ISE) с помощью Logic Apps REST API, чтобы получить доступ к виртуальным сетям Azure (виртуальных сетей) из Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 2c6e35b1e7d160064998004f87c5b14d0eaeac5e
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127656"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Создание среды службы интеграции (ISE) с помощью Logic Apps REST API

В этой статье показано, как создать [ *среду службы интеграции* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) с помощью Logic Apps REST API для сценариев, где приложениям логики и учетным записям интеграции требуется доступ к [виртуальной сети Azure](../virtual-network/virtual-networks-overview.md). ISE — это изолированная среда, использующая выделенное хранилище и другие ресурсы, которые хранятся отдельно от "глобальной" многоклиентской Logic Apps службы. Такое разделение помогает уменьшить любое влияние других клиентов Azure на производительность вашего приложения. Интегрированная среда сценариев также предоставляет собственные статические IP-адреса. Эти IP-адреса отделены от статических IP-адресов, которые совместно используются приложениями логики в общедоступной службе с несколькими клиентами.

Чтобы создать интегрированную среду сценариев с помощью портал Azure, см. статью [Подключение к виртуальным сетям Azure из Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

> [!IMPORTANT]
> Приложения логики, встроенные триггеры, встроенные действия и соединители, работающие в интегрированной среде сценариев, используют тарифный план, отличный от плана ценообразования на основе потребления. Чтобы узнать, как цены и данные о выставлении счетов для Исес, см. [Logic Apps модель ценообразования](../logic-apps/logic-apps-pricing.md#fixed-pricing). Цены см. на странице [цен на Logic Apps](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>предварительные требования

* Те же [условия](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) и [требования, необходимые для обеспечения доступа к интегрированной среде сценариев](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) , как при создании интегрированной среды сценариев в портал Azure

* Средство, которое можно использовать для создания интегрированной среды сценариев путем вызова REST API Logic Apps с запросом HTTPS-размещения. Например, можно использовать [POST](https://www.getpostman.com/downloads/)или создать приложение логики, которое будет выполнять эту задачу.

## <a name="send-the-request"></a>Отправка запроса

Чтобы создать интегрированную среду сценариев, вызвав REST API Logic Apps, сделайте запрос HTTPS to:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Для Logic Apps REST API версии 2019-05-01 необходимо сделать собственный запрос HTTP-размещения для соединителей ISE.

Для завершения развертывания обычно требуется два часа. Иногда развертывание может занять до четырех часов. Чтобы проверить состояние развертывания, в [портал Azure](https://portal.azure.com)на панели инструментов Azure щелкните значок уведомления, чтобы открыть панель уведомлений.

> [!NOTE]
> Если развертывание завершается сбоем или вы удаляете интегрированную среду сценариев, то перед освобождением подсетей Azure может пройти до часа. Эта задержка означает, что может потребоваться подождать, прежде чем повторно использовать эти подсети в другой интегрированной среде сценариев.
>
> При удалении виртуальной сети Azure, как правило, занимает до двух часов, прежде чем выпустить подсети, но эта операция может занять больше времени. 
> При удалении виртуальных сетей убедитесь, что все ресурсы не подключены. 
> См. раздел [Удаление виртуальной сети](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Заголовок запроса

В заголовок запроса включите следующие свойства:

* `Content-type`: задайте для этого свойства значение `application/json`.

* `Authorization`. Задайте для этого свойства токен носителя для клиента, который имеет доступ к подписке Azure или группе ресурсов, которые вы хотите использовать.

### <a name="request-body-syntax"></a>Синтаксис текста запроса

Ниже приведен синтаксис текста запроса, описывающий свойства, используемые при создании интегрированной среды сценариев.

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

### <a name="request-body-example"></a>Пример текста запроса

В этом примере текста запроса показаны примеры значений:

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

* [Добавление ресурсов в среды службы интеграции](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Управление средами службы интеграции](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

