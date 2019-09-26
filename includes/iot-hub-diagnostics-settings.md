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
ms.openlocfilehash: 2498711a5b7e5bce29cd0054ba40257f8f996d43
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266829"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Включение ведения журнала с параметрами диагностики

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к своему Центру Интернета вещей.

2. Выберите **Параметры диагностики**.

3. Выберите **Включить диагностику**.

   ![Включить диагностику](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

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
   * Потоки устройств
   * Метрики устройств

6. Сохраните новые настройки. 

Если вы хотите включить параметры диагностики с помощью PowerShell, используйте следующий код.

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Новые параметры вступят в силу в течение 10 минут. После этого журналы появятся в настроенной цели для архивирования в колонке **Журналы диагностики**. Дополнительные сведения о настройке диагностики см. в статье [Сбор и использование данных журнала из ресурсов Azure](../articles/azure-monitor/platform/resource-logs-overview.md).
