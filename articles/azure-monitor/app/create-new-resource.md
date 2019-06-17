---
title: Создание ресурса Azure Application Insights | Документация Майкрософт
description: Вручную настройте мониторинг Application Insights для нового работающего приложения.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbullwin
ms.openlocfilehash: 9da52e5a9dfa3b55431d66ed3162172226f71a40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073291"
---
# <a name="create-an-application-insights-resource"></a>Создание ресурса Application Insights

В Azure Application Insights данные о приложении отображаются в *ресурсе* Microsoft Azure. Таким образом, создание ресурса является частью [настройки Application Insights для мониторинга нового приложения][start]. После создания нового ресурса, можно получить его ключ инструментирования и используйте его для настройки пакета SDK Application Insights. Ключ инструментирования связывает данные телеметрии в ресурс.

## <a name="sign-in-to-microsoft-azure"></a>Войдите Microsoft Azure

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="create-an-application-insights-resource"></a>Создание ресурса Application Insights

Войдите в [портала Azure](https://portal.azure.com)и создайте ресурс Application Insights:

![Щелкните знак «+» в левом верхнем углу. Выберите Инструменты разработчика, а затем Application Insights](./media/create-new-resource/new-app-insights.png)

   | Параметры        |  Значение           | Описание  |
   | ------------- |:-------------|:-----|
   | **Имя**      | Глобально уникальное значение | Имя, идентифицирующее приложение, которое вы наблюдаете. |
   | **Группа ресурсов**     | myResourceGroup      | Имя для новой или существующей группе ресурсов для размещения данных App Insights. |
   | **Location** | Восточная часть США | Выберите ближайшее расположение или расположение вблизи размещения приложения. |

Введите соответствующие значения в обязательные поля, а затем выберите **проверки + создать**.

![Введите значения в обязательных полях и выберите «Просмотр и создание».](./media/create-new-resource/review-create.png)

После создания приложения, откроется новая панель. Эта панель доступна, где вы см. в разделе производительности и использовании данных об отслеживаемых приложения. 

## <a name="copy-the-instrumentation-key"></a>Копирование ключа инструментирования

Ключ инструментирования идентифицирует ресурс, который необходимо связать данные телеметрии с. Вам потребуется копирования, чтобы добавить ключ инструментирования кода приложения.

![Выберите и скопируйте ключ инструментирования](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Установка пакета SDK в приложении

Установите пакет SDK Application Insights в приложении. Выполнение этого шага зависит от типа приложения.

Используйте ключ инструментирования для настройки [пакета SDK, который можно установить в приложении][start].

Пакет SDK включает стандартные модули, которые отправляют данные телеметрии без необходимости написания дополнительного кода. Для более подробного отслеживания действий пользователей или диагностики неполадок отправляйте собственные данные телеметрии, [используя API][api].

## <a name="creating-a-resource-automatically"></a>Автоматическое создание ресурса
Вы можете написать [Сценарий PowerShell](../../azure-monitor/app/powershell.md) для автоматического создания ресурса.

## <a name="next-steps"></a>Дальнейшие действия
* [Поиск по журналу диагностики](../../azure-monitor/app/diagnostic-search.md)
* [Изучение метрик](../../azure-monitor/app/metrics-explorer.md)
* [Написание запросов аналитики](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md