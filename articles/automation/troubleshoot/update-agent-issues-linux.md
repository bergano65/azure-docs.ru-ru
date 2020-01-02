---
title: Общие сведения о работоспособности гибридной рабочей роли Runbook Linux в Azure Управление обновлениями
description: Узнайте, как устранять неполадки гибридной рабочей роли Runbook в Linux, поддерживающей Управление обновлениями.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 924c2fd176b5b8e45352d616d226f484e814450d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849265"
---
# <a name="understand-the-linux-hybrid-runbook-worker-health-in-update-management"></a>Общие сведения о работоспособности гибридной рабочей роли Runbook Linux в Управление обновлениями

Есть множество причин, по которым для компьютера не отображается состояние **Готово** в службе "Управление обновлениями". В Управление обновлениями можно проверить работоспособность агента гибридной рабочей роли Runbook, чтобы определить базовую проблему. В этой статье описывается, как запустить средство устранения неполадок для компьютеров Azure с портал Azure и с компьютеров, не относящихся к Azure, в [автономном сценарии](#troubleshoot-offline).

В списке ниже приведены три состояния готовности, в которых может находиться компьютер:

* **Готово** . Гибридная Рабочая роль Runbook развертывается и последний раз наблюдался менее 1 часа назад.
* **Disconnected** — Гибридная Рабочая роль Runbook развертывается и последний раз наблюдался в течение 1 часа назад.
* **Не настроено** — Гибридная Рабочая роль Runbook не найдена или не завершила подключение.

> [!NOTE]
> Между отображением портал Azure и текущим состоянием компьютера может возникнуть небольшая задержка.

## <a name="start-the-troubleshooter"></a>Запуск средства устранения неполадок

Для компьютеров Azure: на портале щелкните ссылку **Устранение неполадок** в столбце **Update Agent Readiness** (Готовность агента обновления), после чего откроется страница **Troubleshoot Update Agent** (Устранение неполадок с агентом обновления). Для компьютеров, не относящихся к Azure, ссылка приводится к этой статье. См. инструкции в автономном режиме для устранения неполадок на компьютере, отличном от Azure.

![Страница со списком виртуальных машин](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Проверка выполняется с запущенной виртуальной машиной. Если виртуальная машина не запущена, появится кнопка **Start the VM** (Запустить виртуальную машину).

На странице **Troubleshoot Update Agent** (Устранение неполадок с агентом обновления) выберите **Run Checks** (Выполнить проверки), чтобы запустить средство устранения неполадок. Средство устранения неполадок использует [команду Run](../../virtual-machines/linux/run-command.md) для выполнения сценария на компьютере для проверки зависимостей. После того как средство устранения неполадок завершило свою работу, оно возвращает результаты проверок.

![Страница "Устранение неполадок"](../media/update-agent-issues-linux/troubleshoot-page.png)

По завершении проверок в окне появляются результаты. Разделы о проверках предоставляют сведения о предназначении каждой проверки.

![Страница проверки агента обновлений](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Проверка предварительных требований

### <a name="operating-system"></a>Операционная система

Проверка операционной системы проверяет, работает ли Гибридная Рабочая роль Runbook с одной из следующих операционных систем:

|Операционная система  |Заметки  |
|---------|---------|
|CentOS 6 (x86 или x64) и 7 (x64).      | У агентов Linux должен быть доступ к репозиторию обновлений. Для исправления на основе классификации требуется, чтобы программа yum вернула данные безопасности, которых нет в CentOS, без дополнительной настройки.         |
|Red Hat Enterprise 6 (x86 или x64) и 7 (x64).     | У агентов Linux должен быть доступ к репозиторию обновлений.        |
|SUSE Linux Enterprise Server 11 (x86 или x64) и 12 (x64)     | У агентов Linux должен быть доступ к репозиторию обновлений.        |
|Ubuntu 14.04 LTS, 16.04 LTS и 18.04 LTS (x86/x64)      |У агентов Linux должен быть доступ к репозиторию обновлений.         |

## <a name="monitoring-agent-service-health-checks"></a>Проверки работоспособности службы агента мониторинга

### <a name="log-analytics-agent"></a>Агент Log Analytics

Эта проверка гарантирует, что агент Log Analytics для Linux установлен. Инструкции по его установке см. в разделе [Установка агента для Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="log-analytics-agent-status"></a>Состояние агента Log Analytics

Эта проверка гарантирует, что агент Log Analytics для Linux работает. Если агент не выполняется, можно запустить следующую команду, чтобы попытаться перезапустить его. Дополнительные сведения об устранении неполадок агента см. в разделе об [устранении неполадок с гибридными рабочими ролями runbook для Linux](hybrid-runbook-worker.md#linux).

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Множественная адресация

Эта проверка определяет отправление отчетов агентом в несколько рабочих областей. Множественная адресация не поддерживается службой "Управление обновлениями".

### <a name="hybrid-runbook-worker"></a>Гибридная рабочая роль runbook

Эта проверка проверяет, имеет ли агент Log Analytics для Linux пакет гибридной рабочей роли Runbook. Этот пакет необходим для работы службы "Управление обновлениями".

### <a name="hybrid-runbook-worker-status"></a>Состояние гибридной рабочей роли Runbook

Эта проверка гарантирует, что гибридная рабочая роль Runbook выполняется на компьютере. Следующие процессы должны присутствовать, если гибридная рабочая роль Runbook работает правильно. Дополнительные сведения см. в разделе [Сценарий: агент OMS для Linux не запущен](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Проверка подключения

### <a name="general-internet-connectivity"></a>Общее подключения к Интернету

Проверка позволяет убедиться, что у компьютера есть доступ к Интернету.

### <a name="registration-endpoint"></a>Конечная точка регистрации

Эта проверка определяет, может ли Гибридная Рабочая роль Runbook правильно взаимодействовать с Log Analytics рабочей областью службы автоматизации Azure.

В конфигурации прокси-сервера и брандмауэра необходимо разрешить агенту гибридной рабочей роли Runbook взаимодействовать с конечной точкой регистрации. Список адресов и портов, которые необходимо открыть, см. в разделе [Гибридная рабочая роль](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Конечная точка операций

Эта проверка определяет, может ли агент правильно взаимодействовать со службой Job Runtime Data.

В конфигурации прокси-сервера и брандмауэра необходимо разрешить агенту гибридной рабочей роли Runbook взаимодействовать со службой Job Runtime Data. Список адресов и портов, которые необходимо открыть, см. в разделе [Гибридная рабочая роль](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="log-analytics-endpoint-1"></a>Конечная точка 1 Log Analytics

Проверка подтверждает, что у компьютера есть доступ к конечным точкам, в которых нуждается агент Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Конечная точка 2 Log Analytics

Проверка подтверждает, что у компьютера есть доступ к конечным точкам, в которых нуждается агент Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Конечная точка 3 Log Analytics

Проверка подтверждает, что у компьютера есть доступ к конечным точкам, в которых нуждается агент Log Analytics.

## <a name="troubleshoot-offline"></a>Автономное устранение неполадок

Средство устранения неполадок можно использовать в гибридной рабочей роли в автономном режиме, запустив сценарий локально. Скрипт Python [update_mgmt_health_check.py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) можно найти в центре скриптов. Ниже показан пример выходных данных этого сценария:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Дальнейшие действия

Сведения об устранении дополнительных проблем с гибридными рабочими ролями Runbook см. в разделе [Устранение неполадок гибридных рабочих ролей Runbook](hybrid-runbook-worker.md).
