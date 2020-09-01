---
title: Создание сред службы интеграции (Исес) с помощью Logic Apps REST API
description: Создайте среду службы интеграции (ISE) с помощью Logic Apps REST API, чтобы получить доступ к виртуальным сетям Azure (виртуальных сетей) из Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 427b488fe6673bef505fccdaa7185d69437bceaf
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231322"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Создание среды службы интеграции (ISE) с помощью REST API Logic Apps 

В этой статье показано, как создать [ *среду службы интеграции* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) с помощью Logic Apps REST API для сценариев, где приложениям логики и учетным записям интеграции требуется доступ к [виртуальной сети Azure](../virtual-network/virtual-networks-overview.md). Среда службы интеграции является выделенной средой, в которой используются выделенное хранилище и другие ресурсы, существующие отдельно от "глобальной" мультитенантной службы Logic Apps. Такое разделение помогает уменьшить любое влияние других клиентов Azure на производительность вашего приложения. Среда службы интеграции также предоставляет собственные статические IP-адреса. Эти статические IP-адреса отделены от статических IP-адресов, совместно используемых приложениями логики в общедоступной мультитенантной службе.

Вы также можете создать интегрированную среду сценариев с помощью шаблона быстрого запуска [Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) или с помощью [портал Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

> [!IMPORTANT]
> Приложения логики, встроенные триггеры и соединители, которые выполняются в среде службы интеграции, используют ценовой план, отличный от плана, рассчитанного на потребление. Сведения о ценах и выставлении счетов для сред ISE см. в статье [Модель ценообразования для Azure Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Сведения о тарифах см. на странице [цен на Logic Apps](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Предварительные требования

* Те же [условия](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) и [требования, необходимые для обеспечения доступа к интегрированной среде сценариев](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) , как при создании интегрированной среды сценариев в портал Azure

* Средство, которое можно использовать для создания интегрированной среды сценариев путем вызова REST API Logic Apps с запросом HTTPS-размещения. Например, можно использовать [POST](https://www.getpostman.com/downloads/)или создать приложение логики, которое будет выполнять эту задачу.

## <a name="send-the-request"></a>Отправка запроса

Чтобы создать интегрированную среду сценариев, вызвав REST API Logic Apps, сделайте запрос HTTPS to:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Для Logic Apps REST API версии 2019-05-01 необходимо сделать собственный запрос HTTP-размещения для соединителей ISE.

Для завершения развертывания обычно требуется два часа. Изредка развертывание может занять до четырех часов. Чтобы проверить состояние развертывания, в [портал Azure](https://portal.azure.com)на панели инструментов Azure щелкните значок уведомления, чтобы открыть панель уведомлений.

> [!NOTE]
> Если развертывание завершится ошибкой или вы удалите среду службы интеграции, освобождение подсетей в Azure может занять до одного часа. Такая задержка означает, что вы не сможете сразу применить эти подсети для другой среды службы интеграции.
>
> При удалении виртуальной сети Azure, как правило, требуется до двух часов, прежде чем подсети будут свободны, но эта операция может занять и больше времени. 
> При удалении виртуальных сетей убедитесь, что в них нет подключенных ресурсов. 
> См. статью [Удаление виртуальной сети](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Заголовок запроса

В заголовок запроса включите следующие свойства:

* `Content-type`: Задайте для этого свойства значение `application/json` .

* `Authorization`: Задайте для этого свойства токен носителя для клиента, который имеет доступ к подписке Azure или группе ресурсов, которые вы хотите использовать.

<a name="request-body"></a>

## <a name="request-body"></a>Текст запроса

Ниже приведен синтаксис текста запроса, описывающий свойства, используемые при создании интегрированной среды сценариев. Чтобы создать интегрированную среду сценариев, разрешающую использование самозаверяющего сертификата, установленного в `TrustedRoot` расположении, включите `certificates` объект в раздел описания интегрированной среды сценариев `properties` . Для существующей интегрированной среды сценариев можно отправить запрос PATCH только для `certificates` объекта. Дополнительные сведения об использовании самозаверяющих сертификатов см. [в разделе безопасный доступ и доступ к данным для исходящих вызовов других служб и систем](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests).

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
      },
      // Include `certificates` object to enable self-signed certificate support
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
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
      },
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
   }
}
```

## <a name="next-steps"></a>Дальнейшие действия

* [Добавление ресурсов в среды службы интеграции](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Управление средами службы интеграции](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

