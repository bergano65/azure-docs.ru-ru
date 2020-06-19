---
title: Обзор гибридной рабочей роли Runbook в службе автоматизации Azure
description: Эта статья содержит общие сведения о гибридной рабочей роли Runbook, которую можно использовать для выполнения модулей runbook на компьютерах в локальном центре обработки данных или центре обработки данных поставщика облачных служб.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 9305d0d6443c923c680af0d5fafc58887dadb902
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835297"
---
# <a name="hybrid-runbook-worker-overview"></a>Обзор гибридной рабочей роли Runbook

Модули Runbook в службе автоматизации Azure не будут иметь доступ к ресурсам в других облаках или локальной среде, так как они выполняются на платформе облака Azure. Гибридную рабочую роль Runbook службы автоматизации Azure можно использовать для выполнения модулей runbook непосредственно на компьютере, размещающем роль, для работы с ресурсами в среде, что позволяет управлять этими локальными ресурсами. Для хранения модулей Runbook и управления ими используется служба автоматизации Azure, затем они передаются на один или несколько назначенных компьютеров.

Эта функция проиллюстрирована на следующем изображении.

![Обзор гибридной рабочей роли Runbook](media/automation-hybrid-runbook-worker/automation.png)

Гибридная рабочая роль Runbook может работать под управлением операционной системы Windows или Linux. Чтобы обеспечить мониторинг, необходимо использовать Azure Monitor и агент Log Analytics для поддерживаемой операционной системы. Дополнительные сведения см. в статье [Обзор мониторинга в Microsoft Azure](automation-runbook-execution.md#azure-monitor).

Каждый гибридный компонент Runbook Worker является членом группы гибридных компонентов Runbook Worker, которая указывается при установке агента. Группа может включать одного агента, но в нее можно установить несколько агентов для обеспечения высокого уровня доступности. Каждый компьютер может содержать одну гибридную рабочую роль Runbook, которая создает отчеты для одной учетной записи службы автоматизации. 

Когда вы запускаете модуль runbook в гибридной рабочей роли Runbook, вы выбираете группу для его выполнения. Каждый компонент Worker в группе опрашивает службу автоматизации, чтобы узнать, доступны ли какие-либо задания. Если задание доступно, его получает первая рабочая роль. Время обработки очереди заданий зависит от профиля оборудования и загрузки гибридной рабочей роли. Указание конкретной рабочей роли невозможно. 

Используйте гибридную рабочую роль Runbook вместо [песочницы Azure](automation-runbook-execution.md#runbook-execution-environment), так как у нее нет многих присущих песочнице [ограничений](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) в отношении места на диске, памяти и сетевых сокетов. Ограничения гибридной рабочей роли связаны только с собственными ресурсами рабочей роли. 

> [!NOTE]
> Гибридные рабочие роли Runbook не ограничены пределом времени [справедливого распределения](automation-runbook-execution.md#fair-share), как песочницы Azure. 

## <a name="hybrid-runbook-worker-installation"></a>Установка гибридной рабочей роли Runbook

Процесс установки гибридной рабочей роли Runbook зависит от операционной системы. В таблице ниже указаны типы развертывания.

|Операционная система  |Типы развертывания  |
|---------|---------|
|Windows     | [Автоматическое](automation-windows-hrw-install.md#automated-deployment)<br>[Вручную](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Рекомендуем использовать модуль runbook службы автоматизации для полностью автоматической настройки компьютера Windows. Второй метод заключается в пошаговом выполнении инструкций по ручной установке и настройке роли. Для компьютеров с Linux для установки агента запустите скрипт Python.

## <a name="network-planning"></a><a name="network-planning"></a>Планирование сети

Чтобы гибридная рабочая роль Runbook могла подключаться к службе автоматизации Azure и регистрироваться в ней, ей нужен доступ к номерам портов и URL-адресам, описанным в этом разделе. Кроме того, у рабочей роли должен быть доступ к [портам и URL-адресам, необходимым для того, чтобы агент Log Analytics](../azure-monitor/platform/agent-windows.md) мог подключиться к рабочей области Log Analytics в Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Для гибридной рабочей роли Runbook необходимы следующие порт и URL-адрес:

* Порт: только исходящий интернет-трафик через TCP-порт 443.
* Глобальный URL-адрес: *.azure-automation.net.
* Глобальный URL-адрес US Gov (Вирджиния): *.azure automation.us
* Служба агента: https://\<ИД рабочей области\>.agentsvc.azure-automation.net

При определении [исключений](automation-runbook-execution.md#exceptions) рекомендуем использовать адреса из списка. Если вам нужны IP-адреса, вы можете скачать [список диапазонов IP-адресов центров обработки данных Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Файл обновляется еженедельно и содержит развернутые в настоящее время диапазоны и все предстоящие изменения диапазонов IP-адресов.

### <a name="dns-records-per-region"></a>Записи DNS для каждого региона

При наличии учетной записи службы автоматизации, определенной для конкретного региона, можно ограничить обмен данными гибридной рабочей роли Runbook соответствующим региональным центром обработки данных. В таблице ниже содержатся записи DNS для каждого региона.

| **Регион** | **Запись DNS** |
| --- | --- |
| Центральная Австралия |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Восточная Австралия |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Юго-Восточная Австралия |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Центральная Канада |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Центральная Индия |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| восточная часть США 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Восточная Япония |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Северная Европа |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Центрально-южная часть США |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Юго-Восточная Азия |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| южная часть Соединенного Королевства | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov (Вирджиния) | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| центрально-западная часть США | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Западная Европа |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| западная часть США 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-su1.azure-automation.net |

Для списка IP-адресов региона вместо его имен скачайте XML-файл [IP-адресов центра обработки данных Azure](https://www.microsoft.com/download/details.aspx?id=41653) из Центра загрузки Майкрософт и ознакомьтесь с ним. Обновленный файл публикуется еженедельно. 

В файле IP-адресов приводится список диапазонов IP-адресов, используемых в центрах обработки данных Microsoft Azure. Он включает диапазоны вычислений, SQL и хранения. Кроме того, в нем отражаются развернутые в настоящее время диапазоны и все предстоящие изменения диапазонов IP-адресов. Новые диапазоны, появившиеся в файле, не будут использоваться в центрах обработки данных по крайней мере одну неделю.

Скачивайте новый файл IP-адресов каждую неделю. и вносите соответствующие изменения на своем сайте, чтобы правильно определять службы, выполняемые в Azure. 

> [!NOTE]
> Если вы используете Azure ExpressRoute, обратите внимание, что этот файл используется для того, чтобы обновлять объявление протокола BGP в пространстве Azure в первую неделю каждого месяца.

### <a name="proxy-server-use"></a>Использование прокси-сервера

При использовании прокси-сервера для обмена данными между службой автоматизации Azure и агентом Log Analytics необходимо убедиться, что соответствующие ресурсы доступны. Время ожидания запросов от гибридной рабочей роли Runbook и служб автоматизации составляет 30 секунд. После трех попыток запрос завершится ошибкой. 

### <a name="firewall-use"></a>Использование брандмауэра

Если доступ к Интернету ограничивается брандмауэром, вам нужно изменить его настройки, чтобы разрешить доступ. Если в качестве прокси-сервера используется шлюз Log Analytics, он должен быть настроен для гибридных рабочих ролей Runbook. См. статью о [настройке шлюза Log Analytics для поддержки гибридных рабочих ролей службы автоматизации](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

## <a name="update-management-on-hybrid-runbook-worker"></a>Управление обновлениями в гибридной рабочей роли Runbook

Если включено [решение по управлению обновлениями](automation-update-management.md) службы автоматизации Azure, любой подключенный к рабочей области Log Analytics компьютер будет автоматически настроен в качестве гибридной рабочей роли Runbook. Каждая рабочая роль может поддерживать модули runbook, связанные с управлением обновлениями. 

Настроенный таким образом компьютер не регистрируется в группах гибридных рабочих ролей Runbook, которые уже определены в учетной записи службы автоматизации. Вы можете добавить компьютер в группу гибридных рабочих ролей Runbook, но при этом нужно использовать одну и ту же учетную запись как для Управления обновлениями, так и для членства в группе гибридной рабочей роли Runbook. Эта функция была добавлена в версии 7.2.12024.0 гибридной рабочей роли Runbook.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Адреса для решения по управлению обновлениями в гибридной рабочей роли Runbook

В дополнение к стандартным адресам и портам, которые требуются гибридной рабочей роли Runbook, для Управления обновлениями необходимы адреса из следующей таблицы. Для связи с этими адресами используется порт 443.

|Azure Public  |Azure для государственных организаций  |
|---------|---------|
|*.ods.opinsights.azure.com     | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Использование State Configuration службы автоматизации Azure в гибридной рабочей роли Runbook

Вы можете использовать [State Configuration службы автоматизации Azure](automation-dsc-overview.md) в гибридной рабочей роли Runbook. Для управления конфигурацией серверов, поддерживающих гибридную рабочую роль Runbook, необходимо добавить эти серверы в качестве узлов DSC. Дополнительные сведения см. в статье [Подключение компьютеров для управления с помощью State Configuration службы автоматизации Azure](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Модули runbook в гибридной рабочей роли Runbook

Возможно, вы используете модули runbook, предназначенные для управления ресурсами на локальном компьютере или в локальной среде, где развернута гибридная рабочая роль Runbook. В этом случае можно выбрать запуск модулей runbook в гибридной рабочей роли, а не в учетной записи службы автоматизации. Модули runbook, выполняемые в гибридной рабочей роли Runbook, по структуре идентичны тем, что выполняются в учетной записи службы автоматизации. См. статью [Запуск модулей Runbook в гибридной рабочей роли Runbook](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Задания гибридной рабочей роли Runbook

Задания гибридной рабочей роли Runbook выполняются в локальной учетной записи **System** в Windows или в учетной записи [nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) в Linux. Служба автоматизации Azure обрабатывает задания в гибридных рабочих ролях Runbook несколько иначе, чем задания, выполняемые в песочницах Azure. См. раздел [Среда выполнения runbook](automation-runbook-execution.md#runbook-execution-environment).

При перезагрузке компьютера, выполняющего гибридную рабочую роль Runbook, все выполняемые задания runbook запускаются заново, с самого начала или с последней контрольной точки, если это runbook рабочих процессов PowerShell. Если задание runbook перезапускается более трех раз, его выполнение приостанавливается.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Разрешения runbook для гибридной рабочей роли Runbook

Модули runbook, запущенные в гибридной рабочей роли Runbook, обращаются к ресурсам за пределами Azure, поэтому они не могут использовать стандартный метод аутентификации модулей runbook в Azure. Модуль runbook предоставляет собственную аутентификацию для локальных ресурсов или настраивает ее с помощью [управляемых удостоверений для ресурсов Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Также вы можете указать учетную запись запуска от имени, чтобы предоставить контекст пользователя для всех модулей runbook.

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать, как настроить модули runbook для автоматизации процессов в локальном центре обработки данных или другой облачной среде, см. статью [Запуск модулей runbook в гибридной рабочей роли Runbook](automation-hrw-run-runbooks.md).
* Дополнительные сведения см. в статье [Устранение неполадок с гибридными рабочими ролями Runbook](troubleshoot/hybrid-runbook-worker.md#general).