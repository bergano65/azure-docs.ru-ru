---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/27/2019
ms.author: cynthn
ms.openlocfilehash: a13ce8d3dc71be83e05fae5bd07f30f413ce59f2
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55148039"
---
Работу виртуальных машин можно отслеживать с помощью сбора, просмотра и анализа данных диагностики и журнала. Для простого [мониторинга](../articles/azure-monitor/overview.md) виртуальной машины вы можете воспользоваться экраном обзора на портале Azure. При помощи [расширений](../articles/virtual-machines/windows/extensions-features.md) можно настроить на виртуальных машинах диагностику для сбора дополнительных данных метрик. Кроме того, вы можете использовать дополнительные возможности мониторинга, такие как [Application Insights](../articles/azure-monitor/app/app-insights-overview.md) и [Log Analytics](../articles/azure-monitor/log-query/log-query-overview.md).

## <a name="diagnostics-and-metrics"></a>Диагностика и метрики 

Можно выполнять настройку и мониторинг сбора [данных диагностики](https://docs.microsoft.com/cli/azure/vm/diagnostics) с помощью [метрик](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) на портале Azure, а также при помощи Azure CLI, Azure PowerShell и программных интерфейсов (API). Например, вы можете просматривать:

- **Отслеживать основные метрики для виртуальной машины.** Основные метрики, представленные на экране обзора на портале Azure, включают показатели использования ЦП и сети, общую емкость диска в байтах и число операций с диском в секунду.

- **Включить сбор диагностических сведений о загрузке и просматривать их с помощью портала Azure.** При передаче собственного образа в Azure или даже при загрузке одного из образов платформ могут возникать проблемы с загрузкой виртуальной машины. Это может происходить по разным причинам. Чтобы включить диагностику загрузки при создании виртуальной машины, нажмите кнопку **Включено** рядом с параметром "Диагностика загрузки" в разделе "Мониторинг" на экране параметров.

    Когда виртуальные машины загружаются, агент системы диагностики записывает выходные данные загрузки и сохраняет их в хранилище Azure. Эти данные можно использовать для устранения неполадок загрузки виртуальной машины. Если вы создаете виртуальную машину с помощью программ командной строки, диагностика загрузки не включается автоматически. Перед включением диагностики загрузки необходимо создать учетную запись хранения для хранения журналов загрузки. Если включить диагностику загрузки на портале Azure, учетная запись хранения создается автоматически.

    Если при создании виртуальной машины вы не включили диагностику загрузки, вы сможете включить ее позже с помощью [Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmbootdiagnostics) или [шаблона Azure Resource Manager](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Включить сбор данных диагностики гостевой ОС.** Когда вы создаете виртуальную машину, можно включить диагностику гостевой ОС на экране параметров. Если включить сбор данных диагностики, в виртуальную машину добавляется [расширение IaaSDiagnostics для Linux](../articles/virtual-machines/linux/diagnostic-extension.md) или [расширение IaaSDiagnostics для Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md). Это позволяет собирать дополнительные данные диска, ЦП и памяти.

    При помощи собранных данных диагностики вы можете настроить для виртуальных машин автоматическое масштабирование. Кроме того, можно настроить журналы для хранения данных и оповещений, которые будут информировать вас о проблемах с производительностью.

## <a name="alerts"></a>Оповещения

На основе метрик производительности можно создавать [оповещения](../articles/azure-monitor/platform/alerts-overview.md). Например, вы можете использовать оповещения для уведомления о том, что средняя загрузка ЦП превышает пороговое значение или показатель свободного места на диске ниже определенного значения. Оповещения можно настроить на [портале Azure](../articles/azure-monitor/platform/alerts-classic-portal.md) с использованием [Azure PowerShell](../articles/azure-monitor/platform/alerts-classic-portal.md#with-powershell) или [Azure CLI](../articles/azure-monitor/platform/alerts-classic-portal.md#with-azure-cli).

## <a name="azure-service-health"></a>Работоспособность служб Azure

[Служба работоспособности служб Azure](../articles/service-health/service-health-overview.md) предоставляет персонализированные инструкции и поддержку при возникновении проблем в работе служб Azure, а также помогает подготовиться к предстоящему плановому обслуживанию. Служба работоспособности служб Azure отправляет пользователям и рабочим группам настраиваемые целевые уведомления.

## <a name="azure-resource-health"></a>Служба работоспособности ресурса Azure

Служба [Работоспособность ресурсов Azure](../articles/service-health/resource-health-overview.md) поможет выполнить диагностику и получить необходимую поддержку, если неполадки Azure влияют на ресурсы. Она представляет сведения о текущем состоянии работоспособности ресурсов и о состоянии работоспособности ресурсов за прошедший период, а также помогает устранить проблемы. Служба работоспособности ресурсов обеспечивает поддержку, если вам необходима помощь в решении проблемы со службой Azure.

## <a name="azure-activity-log"></a>Журнал действий Azure

[Журнал действий Azure](../articles/azure-monitor/platform/activity-logs-overview.md) — это журнал подписки с подробными сведениями о событиях на уровне подписки, которые произошли в Azure. В этот журнал входят различные данные: от операционных данных Azure Resource Manager до обновлений в событиях работоспособности службы. Чтобы просмотреть журнал виртуальной машины, щелкните "Журнал действий" на портале Azure.

Ниже описано несколько доступных операций с журналом действий:

- Создание [оповещения о событии журнала действий Azure](../articles/azure-monitor/platform/activity-logs-overview.md).
- [Потоковая передача журнала в концентратор событий](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) для приема сторонней службой или пользовательским аналитическим решением, например PowerBI.
- Анализ журнала в PowerBI с помощью [пакета содержимого PowerBI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Сохранение журнала в учетную запись хранения](../articles/azure-monitor/platform/archive-activity-log.md) для архивации или проверки вручную. В профиле журнала можно задать время хранения (в днях).

Кроме того, вы можете ознакомиться с данными журнала действий, воспользовавшись [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), [Azure CLI](https://docs.microsoft.com/cli/azure/monitor) или [интерфейсами Monitor REST API](https://docs.microsoft.com/rest/api/monitor/).

[Журналы диагностики Azure](../articles/azure-monitor/platform/diagnostic-logs-overview.md) генерируются виртуальной машиной и содержат подробные и актуальные данные о ее работе. Журналы диагностики отличаются от журналов действий тем, что предоставляют подробные сведения об операциях, которые выполнялись на виртуальной машине.

Ниже описано несколько доступных операций с журналами диагностики:

- Сохранение журналов в [учетную запись хранения](../articles/azure-monitor/platform/archive-diagnostic-logs.md) для аудита или проверки вручную. В параметрах диагностики ресурсов можно задать время хранения (в днях).
- [Потоковая передача журналов в Центры событий](../articles/azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) для обработки в сторонней службе или пользовательском аналитическом решении, например в PowerBI.
- Анализ журналов с помощью [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Расширенный мониторинг

- [Azure Monitor](../articles/azure-monitor/overview.md) — это служба, отслеживающая облачные и локальные среды, чтобы поддерживать уровень их доступности и производительности. Эта служба обеспечивает комплексное решение для сбора, анализа и использования телеметрии из облака и локальных сред. Она поможет вам понять, как выполняются приложения, а также заранее определить проблемы, влияющие на них, и ресурсы, от которых они зависят. На виртуальной машине [Linux](../articles/virtual-machines/linux/extensions-oms.md) или [Windows](../articles/virtual-machines/windows/extensions-oms.md) можно установить расширение, которое устанавливает агент Log Analytics для сбора данных журнала и хранения этих данных в рабочей области Log Analytics.

    Для виртуальных машин Windows и Linux рекомендуемым методом сбора данных журналов является установка агента Log Analytics. Проще всего установить агент Log Analytics на виртуальную машину при помощи [расширения виртуальной машины Log Analytics](../articles/log-analytics/log-analytics-azure-vm-extension.md). Использование расширения упрощает процесс установки и автоматически настраивает агент на отправку данных в указанную вами рабочую область службы Log Analytics. Кроме того, агент обновляется автоматически, обеспечивая наличие новейших компонентов и исправлений.

- [Наблюдатель за сетями](../articles/network-watcher/network-watcher-monitoring-overview.md) позволяет выполнять мониторинг виртуальной машины и связанных с ней ресурсов, которые находятся в одной сети. На виртуальной машине [Linux](../articles/virtual-machines/linux/extensions-nwa.md) или [Windows](../articles/virtual-machines/windows/extensions-nwa.md) можно установить расширения для агента Наблюдателя за сетями.

- [Azure Monitor для виртуальных машин](../articles/azure-monitor/insights/vminsights-overview.md) отслеживает виртуальные машины Azure в масштабе, анализируя производительность и работоспособность виртуальных машин Windows и Linux, включая различные процессы и взаимосвязанные зависимости от других ресурсов и внешних процессов. 

## <a name="next-steps"></a>Дополнительная информация
- Выполните инструкции в статьях [Мониторинг виртуальных машин Windows с помощью Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) или [Мониторинг виртуальных машин Linux в Azure](../articles/virtual-machines/linux/tutorial-monitoring.md).
- Ознакомьтесь с рекомендациями по [мониторингу и диагностике](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
