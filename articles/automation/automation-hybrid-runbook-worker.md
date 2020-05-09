---
title: Общие сведения о гибридной рабочей роли Runbook в службе автоматизации Azure
description: В этой статье представлен обзор гибридной рабочей роли Runbook, которая является функцией службы автоматизации Azure, которую можно использовать для запуска модулей Runbook на компьютерах в локальном центре обработки данных или поставщике облачных служб.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: c02a61366b05fa2750c581a1dedefb0d6239dada
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994766"
---
# <a name="hybrid-runbook-worker-overview"></a>Обзор гибридной рабочей роли Runbook

Модули Runbook в службе автоматизации Azure не будут иметь доступ к ресурсам в других облаках или локальной среде, так как они выполняются на платформе облака Azure. Гибридную рабочую роль Runbook службы автоматизации Azure можно использовать для выполнения модулей runbook непосредственно на компьютере, размещающем роль, для работы с ресурсами в среде, что позволяет управлять этими локальными ресурсами. Для хранения модулей Runbook и управления ими используется служба автоматизации Azure, затем они передаются на один или несколько назначенных компьютеров.

Эта функция проиллюстрирована на следующем изображении.

![Обзор гибридной рабочей роли Runbook](media/automation-hybrid-runbook-worker/automation.png)

Гибридная Рабочая роль Runbook может работать под управлением операционной системы Windows или Linux. Для мониторинга требуется использование Azure Monitor и агента Log Analytics для поддерживаемой операционной системы. Дополнительные сведения см. в статье [Обзор мониторинга в Microsoft Azure](automation-runbook-execution.md#azure-monitor).

Каждый гибридный компонент Runbook Worker является членом группы гибридных компонентов Runbook Worker, которая указывается при установке агента. Группа может включать одного агента, но в нее можно установить несколько агентов для обеспечения высокого уровня доступности. Каждый компьютер может содержать одну учетную запись службы автоматизации, с которой можно создавать отчеты для одного гибридного исполнителя. 

Когда вы запускаете модуль runbook в гибридной рабочей роли Runbook, вы выбираете группу для его выполнения. Каждый компонент Worker в группе опрашивает службу автоматизации, чтобы узнать, доступны ли какие-либо задания. Если задание доступно, его получает первая рабочая роль. Время обработки очереди заданий зависит от профиля оборудования гибридной рабочей роли и нагрузки. Указание конкретной рабочей роли невозможно. 

Используйте гибридную рабочую роль Runbook вместо [песочницы Azure](automation-runbook-execution.md#runbook-execution-environment) , так как она не имеет большого количества [ограничений](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) песочницы для дискового пространства, памяти или сетевых сокетов. Ограничения гибридной рабочей роли связаны только с собственными ресурсами работника. 

> [!NOTE]
> Гибридные рабочие роли Runbook не ограничиваются временем [справедливого общего доступа](automation-runbook-execution.md#fair-share) , установленным в песочницах Azure. 

## <a name="hybrid-runbook-worker-installation"></a>Установка гибридной рабочей роли Runbook

Процесс установки гибридной рабочей роли Runbook зависит от операционной системы. В таблице ниже определяются типы развертывания.

|Операционная система  |Типы развертывания  |
|---------|---------|
|Windows     | [Автоматическая регистрация.](automation-windows-hrw-install.md#automated-deployment)<br>[Вручную](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Рекомендуемый способ установки заключается в том, чтобы полностью автоматизировать процесс настройки компьютера Windows с помощью модуля Runbook службы автоматизации Azure. Второй способ — выполнить пошаговую процедуру установки и настройки роли вручную. Для компьютеров с Linux для установки агента запустите скрипт Python.

## <a name="network-planning"></a><a name="network-planning"></a>Планирование сети

Чтобы Гибридная Рабочая роль Runbook подключались к службе автоматизации Azure и зарегистрировалась ею, она должна иметь доступ к номеру порта и URL-адресам, описанным в этом разделе. Кроме того, Рабочая роль должна иметь доступ к [портам и URL-адресам, необходимым для подключения агента log Analytics](../azure-monitor/platform/agent-windows.md) к рабочей области Azure Monitor log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Для гибридной рабочей роли Runbook требуются следующие порты и URL-адреса:

* Порт: для исходящего доступа в Интернет требуется только TCP 443
* Глобальный URL-адрес: *.azure-automation.net.
* Глобальный URL-адрес US Gov (Вирджиния): *.azure automation.us
* Служба агента: https://\<ИД рабочей области\>.agentsvc.azure-automation.net

Рекомендуется использовать адреса, указанные при определении [исключений](automation-runbook-execution.md#exceptions). Чтобы получить IP-адреса, можно скачать [диапазоны IP-адресов центра обработки данных Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Файл обновляется еженедельно и содержит развернутые в настоящее время диапазоны и все предстоящие изменения диапазонов IP-адресов.

### <a name="dns-records-per-region"></a>Записей DNS на регион

Если у вас есть учетная запись службы автоматизации, которая определена для определенного региона, можно ограничить гибридные рабочие роли Runbook от этого регионального центра обработки данных. В таблице ниже содержатся записи DNS для каждого региона.

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

Для списка IP-адресов региона вместо его имен скачайте XML-файл [IP-адресов центра обработки данных Azure](https://www.microsoft.com/download/details.aspx?id=41653) из Центра загрузки Майкрософт и ознакомьтесь с ним. Обновленный файл IP-адресов публикуется еженедельно. 

В файле IP-адресов перечислены диапазоны IP-адресов, используемые в Microsoft Azure центрах обработки данных. Он включает в себя диапазоны вычислений, SQL и хранилища, а также отражает развернутые в данный момент диапазоны и любые предстоящие изменения в диапазонах IP-адресов. Новые диапазоны, появившиеся в файле, не будут использоваться в центрах обработки данных по крайней мере одну неделю.

Рекомендуется загружать новый файл IP-адресов каждую неделю. и вносите соответствующие изменения на своем сайте, чтобы правильно определять службы, выполняемые в Azure. 

> [!NOTE]
> Если вы используете Azure ExpressRoute, помните, что файл IP-адресов используется для обновления объявления протокол BGP (BGP) пространства Azure в первой неделе каждого месяца.

### <a name="proxy-server-use"></a>Использование прокси-сервера

Если вы используете прокси-сервер для обмена данными между службой автоматизации Azure и агентом Log Analytics, убедитесь, что соответствующие ресурсы доступны. Время ожидания запросов от гибридной рабочей роли Runbook и служб автоматизации составляет 30 секунд. После трех попыток запрос завершится ошибкой. 

### <a name="firewall-use"></a>Использование брандмауэра

Если вы используете брандмауэр для ограничения доступа к Интернету, необходимо настроить брандмауэр для разрешения доступа. При использовании шлюза Log Analytics в качестве прокси-сервера убедитесь, что он настроен для гибридных рабочих ролей Runbook. См. раздел [Настройка шлюза log Analytics для гибридных рабочих ролей службы автоматизации](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

## <a name="update-management-on-hybrid-runbook-worker"></a>Управление обновлениями в гибридной рабочей роли Runbook

Если включена служба автоматизации Azure [Управление обновлениями](automation-update-management.md) , любой компьютер, подключенный к рабочей области log Analytics, автоматически настраивается как гибридная Рабочая роль Runbook. Каждый рабочий процесс может поддерживать модули Runbook, предназначенные для управления обновлениями. 

Компьютер, настроенный таким образом, не зарегистрирован ни в одной группе гибридных рабочих ролей Runbook, которая уже определена в вашей учетной записи службы автоматизации. Вы можете добавить компьютер в группу гибридных рабочих ролей Runbook, но вы должны использовать одну и ту же учетную запись как для Управление обновлениями, так и для членства в группе гибридной рабочей роли Runbook. Эта функция была добавлена в 7.2.12024.0 версии гибридной рабочей роли Runbook.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Управление обновлениями адреса гибридной рабочей роли Runbook

Поверх стандартных адресов и портов, необходимых гибридной рабочей роли Runbook, Управление обновлениями требуются адреса в следующей таблице. Для обмена данными с этими адресами используется порт 443.

|Azure Public  |Azure для государственных организаций  |
|---------|---------|
|*.ods.opinsights.azure.com     | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|

## <a name="state-configuration-dsc-on-hybrid-runbook-worker"></a>Настройка состояния (DSC) в гибридной рабочей роли Runbook

Функцию [настройки состояния (DSC)](automation-dsc-overview.md) можно запустить в гибридной рабочей роли Runbook. Для управления конфигурацией серверов, поддерживающих гибридную рабочую роль Runbook, необходимо добавить эти серверы в качестве узлов DSC. Дополнительные сведения о подключении см. в статье подключение [компьютеров для управления с помощью настройки состояния (DSC)](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Модули Runbook в гибридной рабочей роли Runbook

Возможно, у вас есть модули Runbook, которые управляют ресурсами на локальном компьютере или работают с ресурсами в локальной среде, в которой развернута Гибридная Рабочая роль Runbook. В этом случае можно выбрать Запуск модулей Runbook в гибридной рабочей роли, а не в учетной записи службы автоматизации. Модули Runbook, выполняемые в гибридной рабочей роли Runbook, идентичны в структуре тем, которые выполняются в учетной записи службы автоматизации. См. раздел [Запуск модулей Runbook в гибридной рабочей роли Runbook](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Задания гибридной рабочей роли Runbook

Задания гибридной рабочей роли Runbook выполняются под локальной **системной** учетной записью в Windows или в [учетной записи нксаутоматион](automation-runbook-execution.md#log-analytics-agent-for-linux) в Linux. Служба автоматизации Azure обрабатывает задания в гибридных рабочих ролях Runbook несколько иначе из заданий, выполняемых в изолированных средах Azure. См. раздел [Среда выполнения Runbook](automation-runbook-execution.md#runbook-execution-environment).

Если Гибридный компьютер узла Runbook Worker перезапускается, любое запущенное задание Runbook перезапускается с самого начала или из последней контрольной точки для модулей Runbook рабочего процесса PowerShell. После перезапуска задания Runbook более трех раз оно приостанавливается.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Разрешения Runbook для гибридной рабочей роли Runbook

Так как они обращаются к ресурсам, не относящимся к Azure, модули Runbook, работающие в гибридной рабочей роли Runbook, не могут использовать механизм проверки подлинности, обычно используемый модулями Runbook для проверки подлинности ресурсов Модуль Runbook либо предоставляет собственную проверку подлинности для локальных ресурсов, либо настраивает проверку подлинности с помощью [управляемых удостоверений для ресурсов Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Можно также указать учетную запись запуска от имени, чтобы предоставить контекст пользователя для всех модулей Runbook.

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать, как настроить модули runbook для автоматизации процессов в локальном центре обработки данных или другой облачной среде, см. статью [Запуск модулей runbook в гибридной рабочей роли Runbook](automation-hrw-run-runbooks.md).
* Сведения об устранении неполадок гибридных рабочих ролей Runbook см. в разделе [Устранение неполадок гибридных рабочих ролей Runbook](troubleshoot/hybrid-runbook-worker.md#general).