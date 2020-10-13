---
title: Общие сведения об агенте Log Analytics
description: В этой статье содержатся сведения о том, как собирать данные и наблюдать за компьютерами, размещенными в Azure, локальной или другой облачной среде, с помощью Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 9f0a7b6f68c5a3adeb320fd18bec2f195a833dbf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310005"
---
# <a name="log-analytics-agent-overview"></a>Общие сведения об агенте Log Analytics
Агент Log Analytics Azure собирает данные телеметрии с виртуальных машин Windows и Linux в любом облаке, локальных компьютерах, а также в тех, которые отслеживаются [System Center Operations Manager](/system-center/scom/) и отправляет собранные в log Analytics рабочую область в Azure Monitor. Агент Log Analytics также поддерживает аналитику и другие службы в Azure Monitor, такие как [Azure Monitor для виртуальных машин](../insights/vminsights-enable-overview.md), [Центр безопасности Azure](../../security-center/index.yml) и [Служба автоматизации Azure](../../automation/automation-intro.md). В этой статье приводятся подробные сведения о требованиях к агенту, системе и сети и методах развертывания.

> [!NOTE]
> Агент Log Analytics также называется агентом Microsoft Monitoring Agent (MMA) или агентом OMS Linux.

## <a name="comparison-to-azure-diagnostics-extension"></a>Сравнение с расширением Диагностика Azure
Для получения данных мониторинга из гостевой ОС виртуальной машины Azure вы также можете использовать [Расширение Диагностика Azure](diagnostics-extension-overview.md) в Azure Monitor. Вы можете использовать любое из этих средств или оба сразу в зависимости от конкретных требований. В статье [Общие сведения об агентах Azure Monitor](agents-overview.md) приведено подробное сравнение разных агентов. 

Основные различия:

- Расширение "Диагностика Azure" может работать только с виртуальными машинами Azure. Агент Log Analytics совместим с виртуальными машинами в Azure, других облачных и локальной среде.
- Расширение "Диагностика Azure" отправляет данные в службу хранилища Azure, в [метрики мониторинга Azure](data-platform-metrics.md) (только для ОС Windows) и в Центры событий. Агент Log Analytics отправляет данные в [журналы Azure Monitor](data-platform-logs.md).
- Агент Log Analytics является обязательным компонентом [решений](../monitor-reference.md#insights-and-core-solutions), [Azure Monitor для виртуальных машин](../insights/vminsights-overview.md) и других служб, например [Центра безопасности Azure](../../security-center/index.yml).

## <a name="costs"></a>Затраты
Плата за агента Log Analytics не взимается, но, возможно, вам придется оплатить полученные данные. Дополнительные сведения о ценах на данные, собираемые в рабочей области Log Analytics, см. в статье [Управление использованием и затратами с помощью журналов Azure Monitor](manage-cost-storage.md).

## <a name="supported-operating-systems"></a>Поддерживаемые операционные системы

 Список версий операционных систем Windows и Linux, поддерживаемых агентом Log Analytics, см. в разделе [Поддерживаемые операционные системы](agents-overview.md#supported-operating-systems) . 


## <a name="data-collected"></a>Собираемые данные
В этой таблице перечислены типы собираемых из всех подключенных агентов данных, которые вы можете настроить для рабочей области Log Analytics. Список аналитических сведений, и других решений, использующих агент Log Analytics для получения данных других типов, вы сможете найти в статье [Что отслеживается Azure Monitor?](../monitor-reference.md).

| Источник данных | Описание |
| --- | --- |
| [Журналы событий Windows](data-sources-windows-events.md) | Информация, отправляемая системой ведения журналов событий Windows. |
| [Syslog](data-sources-syslog.md)                     | Информация, отправляемая системой ведения журналов событий Linux. |
| [Производительность](data-sources-performance-counters.md)  | Числовые значения, представляющие собой оценки производительности разных элементов операционной системы и рабочих нагрузок. |
| [Журналы IIS](data-sources-iis-logs.md)                 | Информация о потреблении для веб-сайтов на IIS, которые выполняются в гостевой ОС. |
| [Пользовательские журналы](data-sources-custom-logs.md)           | События из текстовых файлов на компьютерах Windows и Linux. |

## <a name="data-destinations"></a>Целевое расположение данных
Агент Log Analytics отправляет данные в рабочую область Log Analytics в Azure Monitor. Агент Windows может быть общим, отправляя данные в несколько рабочих областей и групп управления System Center Operations Manager. Агент Linux может отправить только в одно место назначения — рабочую область или группу управления.

## <a name="other-services"></a>другие службы.
Агент для Linux и Windows не только подключается к Azure Monitor. Другие службы, такие как центр безопасности Azure и метка Azure, используют агент и подключенную рабочую область Log Analytics. Кроме того, агент поддерживает службу автоматизации Azure для размещения гибридной рабочей роли Runbook и других служб, таких как [Отслеживание изменений](../../automation/change-tracking.md), [Управление обновлениями](../../automation/update-management/update-mgmt-overview.md)и [Центр безопасности Azure](../../security-center/security-center-intro.md). Дополнительные сведения о гибридной рабочей роли Runbook см. в разделе [Общие сведения об архитектуре автоматизации](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="workspace-and-management-group-limitations"></a>Ограничения рабочей области и группы управления

Дополнительные сведения о подключении агента к группе управления Operations Manager см. в статье [Настройка агента для передачи отчетов в группу управления Operations Manager](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group) .

* Агенты Windows могут подключаться к четырем рабочим областям, даже если они подключены к группе управления System Center Operations Manager.
* Агент Linux не поддерживает несколько сетевых интерфейсов и может подключаться только к одной рабочей области или группе управления.


## <a name="security-limitations"></a>Ограничения безопасности

* Агенты Windows и Linux поддерживают [стандарт FIPS 140](/windows/security/threat-protection/fips-140-validation), но [другие типы усиления защиты могут не поддерживаться](agent-linux.md#supported-linux-hardening).


## <a name="installation-options"></a>Варианты установки

Существует несколько методов установки агента Log Analytics и подключения компьютера к Azure Monitor в зависимости от ваших требований. В следующих разделах перечислены возможные методы для различных типов виртуальных машин.
> [!NOTE]
> Клонирование компьютера с уже настроенным агентом Log Analytics не поддерживается. Если агент уже связан с рабочей областью, это не будет работать для "золотых образов".

### <a name="azure-virtual-machine"></a>Виртуальная машина Azure

- [Azure Monitor для виртуальных машин](../insights/vminsights-enable-overview.md) предоставляет несколько методов, позволяющих использовать агенты в масштабе. Сюда входит установка агента Log Analytics и агента зависимостей. 
- [Центр безопасности Azure может подготавливать агент log Analytics](../../security-center/security-center-enable-data-collection.md) на всех поддерживаемых виртуальных машинах Azure и всех новых, созданных, если включить его для отслеживания уязвимостей и угроз безопасности.
- Log Analytics расширение виртуальной машины для [Windows](../../virtual-machines/extensions/oms-windows.md) или [Linux](../../virtual-machines/extensions/oms-linux.md) можно установить с помощью портал Azure, Azure CLI, Azure PowerShell или шаблона Azure Resource Manager.
- Вручную установите для отдельных виртуальных машин Azure [из портал Azure](../learn/quick-collect-azurevm.md?toc=%2fazure%2fazure-monitor%2ftoc.json).


### <a name="windows-virtual-machine-on-premises-or-in-another-cloud"></a>Виртуальная машина Windows в локальной среде или в другом облаке 

- [Вручную установите](agent-windows.md) агент из командной строки.
- Автоматизируйте установку с помощью [Azure Automation DSC](agent-windows.md#install-agent-using-dsc-in-azure-automation).
- Использование [шаблона диспетчер ресурсов с Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win)

### <a name="linux-virtual-machine-on-premises-or-in-another-cloud"></a>Виртуальная машина Linux в локальной среде или в другом облаке

- [Вручную установите](../learn/quick-collect-linux-computer.md) агент, вызывающий скрипт-оболочку, размещенный на GitHub.
- System Center Operations Manager | [Интеграция Operations Manager с Azure Monitor](./om-agents.md) для пересылки собранных данных с компьютеров Windows, отсылаемых в группу управления.

## <a name="workspace-id-and-key"></a>ИДЕНТИФИКАТОР и ключ рабочей области
Независимо от используемого метода установки для рабочей области Log Analytics, к которой будет подключаться агент, потребуется идентификатор и ключ рабочей области. Выберите рабочую область в меню **log Analytics рабочие области** в портал Azure. Затем в разделе **Параметры** выберите **Управление агентами** . 

[![Сведения о рабочей области](media/log-analytics-agent/workspace-details.png)](media/log-analytics-agent/workspace-details.png#lightbox)

## <a name="tls-12-protocol"></a>Протокол TLS 1.2

Чтобы обеспечить безопасность передаваемых данных в журналы Azure Monitor, настоятельно рекомендуем настроить для агента использование протокола TLS как минимум версии 1.2. Более старые версии протоколов TLS/SSL оказались уязвимы. Хотя они все еще используются для обеспечения обратной совместимости, применять их **не рекомендуется**.  См. дополнительные сведения о [безопасной отправке данных с помощью TLS 1.2](data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-requirements"></a>Требования к сети
Агент для Linux и Windows передает исходящий трафик в службу Azure Monitor через TCP-порт 443. Если компьютер подключается через брандмауэр или прокси-сервер для обмена данными через Интернет, ознакомьтесь с приведенными ниже требованиями, чтобы понять необходимую конфигурацию сети. Если политики безопасности ИТ не позволяют компьютерам в сети подключаться к Интернету, можно настроить [шлюз log Analytics](gateway.md) , а затем настроить агент для подключения через шлюз к Azure Monitor. Затем агент может получать сведения о конфигурации и отправлять собранные данные.

![Схема связей агента Log Analytics](./media/log-analytics-agent/log-analytics-agent-01.png)

В следующей таблице перечислены сведения о конфигурации прокси-сервера и брандмауэра, необходимые агентам Linux и Windows для взаимодействия с журналами Azure Monitor.

### <a name="firewall-requirements"></a>Требования к брандмауэру

|Ресурс агента|порты; |Направление |Обход проверки HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Порт 443 |Исходящие|Да |  
|*.oms.opinsights.azure.com |Порт 443 |Исходящие|Да |  
|*.blob.core.windows.net |Порт 443 |Исходящие|Да |
|*.azure-automation.net |Порт 443 |Исходящие|Да |

Сведения о брандмауэре, необходимые для управления Azure для государственных организаций, см. в [здесь](../../azure-government/compare-azure-government-global-azure.md#azure-monitor). 

Если планируется использование Azure Automation Hybrid Runbook Worker для подключения к службе автоматизации и регистрации в ней, чтобы применить runbook или решения для управления в вашей среде, они должны иметь доступ к номеру порта и URL-адресам, описанным в разделе [Настройка сети для Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

### <a name="proxy-configuration"></a>Конфигурация прокси-сервера

Агенты Windows и Linux поддерживают обмен данными со службой Azure Monitor через прокси-сервер или шлюз Log Analytics по протоколу HTTPS.  Поддерживается анонимная и базовая аутентификация (с именем пользователя и паролем).  Для агента Windows, подключенного непосредственно к службе, конфигурация прокси-сервера указывается во время установки или [после развертывания](agent-manage.md#update-proxy-settings) — в панели управления или с помощью PowerShell.  

Для агента Linux прокси-сервер определяется во время или [после установки](agent-manage.md#update-proxy-settings), или при изменении файла конфигурации proxy.conf.  Конфигурация прокси-сервера агента Linux имеет следующий синтаксис:

`[protocol://][user:password@]proxyhost[:port]`

|Свойство| Описание |
|--------|-------------|
|Протокол | HTTPS |
|пользователь | Необязательное имя пользователя для аутентификации прокси-сервера |
|password | Необязательный пароль для аутентификации прокси-сервера |
|proxyhost | Адрес или полное доменное имя прокси-сервера или шлюза Log Analytics |
|порт | Необязательный номер порта прокси-сервера или шлюза Log Analytics |

Например: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Если в пароле использованы специальные знаки (например, \@), отобразится ошибка подключения прокси-сервера из-за неправильного анализа значения.  Чтобы обойти эту проблему, закодируйте пароль в URL-адрес, используя специальные инструменты (например, [URLDecode](https://www.urldecoder.org/)).  



## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения об источниках данных, доступных для сбора данных из операционных систем Windows или Linux, см. в статье [Data sources in Log Analytics](agent-data-sources.md) (Источники данных в Log Analytics). 
* Узнайте больше о [запросах журнала](../log-query/log-query-overview.md), которые можно применять для анализа данных, собираемых из источников данных и решений. 
* Узнайте больше о [решениях мониторинга](../insights/solutions.md), которые расширяют функции службы Azure Monitor и собирают данные в ее рабочей области.

