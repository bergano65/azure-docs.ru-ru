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
ms.openlocfilehash: 6f7772eb7f2c500bbb58c391b1bc4b7a73141699
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675761"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Включение ведения журнала с параметрами диагностики

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
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Новые параметры вступят в силу в течение 10 минут. После этого журналы появятся в настроенной цели для архивирования в колонке **Журналы диагностики**. Дополнительные сведения о настройке диагностики см. в статье [Сбор и использование данных журнала из ресурсов Azure](../articles/azure-monitor/platform/diagnostic-logs-overview.md).