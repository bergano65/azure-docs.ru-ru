---
title: Обзор расширения Системы диагностики Azure
description: Диагностику Azure можно использовать для отладки, оценки производительности, мониторинга, а также анализа трафика в облачных службах, на виртуальных машинах и в Service Fabric.
author: rboucher
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: e0325a3bda912c95d8d27646bc1e80fff5ce10a8
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639424"
---
# <a name="what-is-azure-diagnostics-extension"></a>Расширение Диагностики Azure
Расширение диагностики Azure — это агент, который позволяет выполнять сбор диагностических данных о развернутом приложении. Можно использовать модуль диагностики из различных источников. Сейчас поддерживаются веб-роль и рабочая роль облачной службы Azure (классические), виртуальные машины, масштабируемые наборы виртуальных машин и Service Fabric. Для других служб Azure предусмотрены другие методы диагностики. Ознакомьтесь с [общими сведениями о мониторинге в Azure](../../azure-monitor/overview.md).

## <a name="linux-agent"></a>Агент Linux
[Linux-версия расширения](../../virtual-machines/extensions/diagnostics-linux.md) доступна для виртуальных машин под управлением ОС Linux. Собранные данные статистики и поведения могут отличаться от тех, которые были собраны в версии для Windows.

## <a name="data-you-can-collect"></a>Собираемые данные
Расширение системы диагностики Azure может собирать следующие типы данных:

| источника данных | Описание |
| --- | --- |
| Метрики счетчиков производительности |Системные и пользовательские счетчики производительности |
| Журналы приложений |Сообщения трассировки, записанные вашим приложением |
| Журналы событий Windows |Информация, отправляемая системой ведения журналов событий Windows |
| Журналы .NET EventSource |События записи кода с использованием класса .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) |
| Журналы IIS |Информация о веб-сайтах IIS |
| [Журналы трассировки событий Windows на основе манифеста](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |События трассировки событий Windows, созданные любым процессом. (1) |
| Аварийные дампы (журналы) |Информация о состоянии процесса в случае сбоя приложения |
| Пользовательские журналы ошибок |Журналы, созданные вашим приложением или службой |
| Журналы инфраструктуры системы диагностики Azure |Информация о самой системе диагностики Azure |

(1) Чтобы получить список поставщиков трассировки событий Windows, выполните `c:\Windows\System32\logman.exe query providers` в окне консоли на компьютере, с которого требуется собирать информацию.

## <a name="data-storage"></a>Хранилище данных
Расширение хранит свои данные в указанной [учетной записи службы хранилища Azure](diagnostics-extension-to-storage.md).

Также данные можно отправить в [Application Insights](../../azure-monitor/app/cloudservices.md). 

Другим вариантом является их потоковая передача в [концентратор событий](../../event-hubs/event-hubs-about.md), позволяющий отправить данные службам мониторинга, не связанным с Azure.

Вы также можете отправить данные в базы данных временных рядов метрик Azure Monitor. Сейчас приемник Azure Monitor работает только с данными счетчиков производительности. Он позволяет отправлять счетчики производительности в пользовательские метрики. Эта функция предоставляется в предварительной версии. Приемник Azure Monitor поддерживает такие функции:
* Получение данных всех счетчиков производительности, отправляемых в Azure Monitor с помощью [API метрик Azure Monitor](https://docs.microsoft.com/rest/api/monitor/).
* Оповещения на всех счетчиках производительности, отправляемых в Azure Monitor, с помощью [оповещений метрики](../../azure-monitor/platform/alerts-overview.md) в Azure Monitor.
* Трактование оператора подстановочного знака в счетчиках производительности как экземпляра в метрике.  Например, если вы собираете данные счетчика LogicalDisk(\*)/DiskWrites/sec (Количество записей на логический диск в секунду), вы можете отфильтровать и распределить данные в измерении "Экземпляр", чтобы построить график или настроить оповещения для каждого логического диска виртуальной машины (например, C:).

Дополнительные сведения о том, как настроить этот приемник, см. в статье [Схема конфигурации системы диагностики Azure версии 1.3 и более поздней](diagnostics-extension-schema-1dot3.md).

## <a name="costs"></a>Стоимость
Каждый из приведенных выше вариантов может повлечь расходы. Обязательно изучите их, чтобы избежать непредвиденных векселей.  Application Insights, концентратор событий и хранилище Azure имеют отдельные затраты, связанные с приемом и временем хранения. В частности, служба хранилища Azure будет постоянно хранить все данные, поэтому вам может потребоваться очистить старые данные по истечении определенного периода времени, чтобы снизить затраты.    

## <a name="versioning-and-configuration-schema"></a>Управление версиями и конфигурация схемы
Дополнительные сведения см. в статье [Azure Diagnostics Version History and Schema](diagnostics-extension-schema.md) (Журнал и схема версий системы диагностики Azure).


## <a name="next-steps"></a>Следующие шаги
Выберите службы, в которых вы хотите собирать данные диагностики, и выполните действия, описанные в перечисленных ниже статьях. Справку по отдельным задачам см. в общих статьях о диагностике Azure.

## <a name="cloud-services-using-azure-diagnostics"></a>Облачные службы с использованием диагностики Azure
* Если используется Visual Studio, см. статью [Отладка облачной службы или виртуальной машины Azure в Visual Studio](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines). В остальных случаях см. следующие статьи:
* [Мониторинг облачных служб с помощью Диагностики Azure](../../cloud-services/cloud-services-how-to-monitor.md)
* [Включение системы диагностики Azure в облачных службах Azure](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Более подробные сведения см. в статьях:

* [Использование Диагностики Azure с Application Insights для облачных служб Azure](../../azure-monitor/app/cloudservices.md)
* [Трассировка потока в приложении облачных служб с помощью системы диагностики Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Включение системы диагностики Azure на виртуальной машине под управлением Windows с помощью PowerShell](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Виртуальные машины
* Если используется Visual Studio, см. статью [Отладка облачной службы или виртуальной машины Azure в Visual Studio](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines). В остальных случаях см. следующие статьи:
* [Включение Диагностики Azure на виртуальных машинах Azure](/azure/virtual-machines/extensions/diagnostics-windows)

Более подробные сведения см. в статьях:

* [Включение системы диагностики Azure на виртуальной машине под управлением Windows с помощью PowerShell](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Создание виртуальной машины Windows с мониторингом и диагностикой с использованием шаблона Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Платформа Service Fabric
Начните со статьи [Мониторинг и диагностика состояния служб в локальной среде разработки](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). В дереве навигации слева от нее вы найдете ссылки на множество других статей по диагностике Service Fabric.

## <a name="general-articles"></a>Общие статьи
* Узнайте, как [использовать счетчики производительности в Диагностике Azure](../../cloud-services/diagnostics-performance-counters.md).
* Если возникнут проблемы с запуском диагностики или поиском данных в таблицах хранилища Azure, см. статью [Устранение неполадок с помощью системы диагностики Azure](diagnostics-extension-troubleshooting.md).

