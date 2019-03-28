---
title: включение файла
description: включение файла
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: dd5dc53311c8611a4ca4d174401bba797fe5c4b1
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505731"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Включение ведения журнала с параметрами диагностики

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к своему Центру Интернета вещей.

2. Выберите **Параметры диагностики**.

3. Выберите **Включить диагностику**.

   ![Включение диагностики](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Присвойте имя параметрам диагностики.

5. Выберите назначение для отправки журналов. Можно выбрать любое сочетание трех параметров:

   * "Архивировать в учетной записи хранения";
   * "Передать в концентратор событий";
   * "Отправить в Log Analytics".

6. Выберите операции, которые требуется отслеживать, и включите журналы для этих них. Ниже перечислены операции, для которых доступны параметры диагностики:

   * Подключения
   * Телеметрия устройства
   * Получение сообщений из облака на устройство
   * Операции с удостоверениями устройства
   * Передача файлов
   * Маршрутизация сообщений
   * Операции переноса из облака на двойник устройства
   * Операции переноса с двойника устройства в облако
   * операции двойников;
   * операции заданий;
   * Прямые методы  
   * Распределенная трассировка (предварительная версия)
   * Конфигурации
   * Потоки устройства
   * Метрики устройства

6. Сохраните новые настройки. 

Если вы хотите включить параметры диагностики с помощью PowerShell, используйте следующий код.

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Новые параметры вступят в силу в течение 10 минут. После этого журналы появятся в настроенной цели для архивирования в колонке **Журналы диагностики**. Дополнительные сведения о настройке диагностики см. в статье [Сбор и использование данных журнала из ресурсов Azure](../articles/azure-monitor/platform/diagnostic-logs-overview.md).
