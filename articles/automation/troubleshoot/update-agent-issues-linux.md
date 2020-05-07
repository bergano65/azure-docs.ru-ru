---
title: Устранение проблем с агентом обновления Linux в службе автоматизации Azure Управление обновлениями
description: Узнайте, как устранять неполадки и устранять проблемы с агентом обновления Windows для Linux с помощью решения Управление обновлениями.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: dadfe0022cfb99703222ba7a91ca3ec6f5fce645
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836637"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Устранение проблем с агентом обновления Linux

Может быть несколько причин, по которым компьютер не отображается как готовый (работоспособный) в решении Управление обновлениями службы автоматизации Azure. В Управление обновлениями можно проверить работоспособность агента гибридной рабочей роли Runbook, чтобы определить базовую проблему. В этой статье описывается, как запустить средство устранения неполадок для компьютеров Azure с портал Azure и с компьютеров, не относящихся к Azure, в [автономном сценарии](#troubleshoot-offline). 

Компьютер может находиться в трех состояниях готовности:

* **Готово**. Гибридная Рабочая роль Runbook развертывается и последний раз наблюдался менее одного часа назад.
* **Отключено**. Гибридная Рабочая роль Runbook развертывается и последний раз наблюдался за один час назад.
* **Не настроено**: Гибридная Рабочая роль Runbook не найдена или не завершила подключение.

> [!NOTE]
> Между отображением портал Azure и текущим состоянием компьютера может быть небольшая задержка.

## <a name="start-the-troubleshooter"></a>Запуск средства устранения неполадок

Для компьютеров Azure выберите ссылку " **Устранение неполадок** " в столбце **готовность агента обновления** на портале, чтобы открыть страницу **Устранение неполадок агента обновления** . Для компьютеров, не относящихся к Azure, ссылка приводится к этой статье. Инструкции по устранению неполадок на компьютерах, не относящихся к Azure, см. в разделе "Устранение неполадок в автономном режиме".

![Страница списка виртуальных машин](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Проверка выполняется с запущенной виртуальной машиной. Если виртуальная машина не запущена, **Запустите виртуальную машину** .

На странице **Устранение неполадок агента обновления** выберите **выполнить проверки** , чтобы запустить средство устранения неполадок. Средство устранения неполадок использует [команду Run](../../virtual-machines/linux/run-command.md) для выполнения сценария на компьютере для проверки зависимостей. После того как средство устранения неполадок завершило свою работу, оно возвращает результаты проверок.

![Страница "Устранение неполадок"](../media/update-agent-issues-linux/troubleshoot-page.png)

После завершения проверок результаты возвращаются в окне. Разделы о проверках предоставляют сведения о предназначении каждой проверки.

![Страница проверки агента обновлений](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Проверки готовности

### <a name="operating-system"></a>Операционная система

Проверка операционной системы проверяет, работает ли Гибридная Рабочая роль Runbook с одной из следующих операционных систем.

|Операционная система  |Примечания  |
|---------|---------|
|CentOS 6 (x86 или x64) и 7 (x64).      | У агентов Linux должен быть доступ к репозиторию обновлений. Для установки исправлений на основе классификации требуется, чтобы "Yum" возвращал данные безопасности, которые CentOS не являются готовыми.         |
|Red Hat Enterprise 6 (x86 или x64) и 7 (x64).     | У агентов Linux должен быть доступ к репозиторию обновлений.        |
|SUSE Linux Enterprise Server 11 (x86 или x64) и 12 (x64)     | У агентов Linux должен быть доступ к репозиторию обновлений.        |
|Ubuntu 14.04 LTS, 16.04 LTS и 18.04 LTS (x86/x64)      |У агентов Linux должен быть доступ к репозиторию обновлений.         |

## <a name="monitoring-agent-service-health-checks"></a>Проверки работоспособности службы агента мониторинга

### <a name="log-analytics-agent"></a>Агент Log Analytics

Эта проверка гарантирует, что агент Log Analytics для Linux установлен. Инструкции по его установке см. в разделе [Установка агента для Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux).

### <a name="log-analytics-agent-status"></a>Состояние агента Log Analytics

Эта проверка гарантирует, что агент Log Analytics для Linux работает. Если агент не выполняется, можно запустить следующую команду, чтобы попытаться перезапустить его. Дополнительные сведения об устранении неполадок агента см. в [статье Устранение неполадок гибридной рабочей роли Runbook Linux](hybrid-runbook-worker.md#linux).

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Множественная адресация

Эта проверка определяет отправление отчетов агентом в несколько рабочих областей. Поддержка нескольких сетевых интерфейсов не поддерживается Управление обновлениями.

### <a name="hybrid-runbook-worker"></a>Гибридная рабочая роль Runbook

Эта проверка проверяет, имеет ли агент Log Analytics для Linux пакет гибридной рабочей роли Runbook. Этот пакет необходим для работы службы "Управление обновлениями".

### <a name="hybrid-runbook-worker-status"></a>Состояние гибридной рабочей роли Runbook

Эта проверка гарантирует, что гибридная рабочая роль Runbook выполняется на компьютере. Следующие процессы должны присутствовать, если гибридная рабочая роль Runbook работает правильно. Дополнительные сведения см. в разделе [Устранение неполадок агента log Analytics для Linux](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Проверка подключения

### <a name="general-internet-connectivity"></a>Общее подключения к Интернету

Проверка позволяет убедиться, что у компьютера есть доступ к Интернету.

### <a name="registration-endpoint"></a>Конечная точка регистрации

Эта проверка определяет, может ли Гибридная Рабочая роль Runbook правильно взаимодействовать со службой автоматизации Azure в рабочей области Log Analytics.

В конфигурации прокси-сервера и брандмауэра необходимо разрешить агенту гибридной рабочей роли Runbook взаимодействовать с конечной точкой регистрации. Список адресов и портов для открытия см. в статье [сетевое планирование для гибридных рабочих ролей](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Конечная точка операций

Эта проверка определяет, может ли агент правильно взаимодействовать со службой Job Runtime Data.

В конфигурации прокси-сервера и брандмауэра необходимо разрешить агенту гибридной рабочей роли Runbook взаимодействовать со службой Job Runtime Data. Список адресов и портов для открытия см. в статье [сетевое планирование для гибридных рабочих ролей](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="log-analytics-endpoint-1"></a>Конечная точка 1 Log Analytics

Проверка подтверждает, что у компьютера есть доступ к конечным точкам, в которых нуждается агент Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Конечная точка 2 Log Analytics

Проверка подтверждает, что у компьютера есть доступ к конечным точкам, в которых нуждается агент Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Конечная точка 3 Log Analytics

Проверка подтверждает, что у компьютера есть доступ к конечным точкам, в которых нуждается агент Log Analytics.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Автономное устранение неполадок

Средство устранения неполадок можно использовать в гибридной рабочей роли в автономном режиме, запустив сценарий локально. Сценарий Python, [update_mgmt_health_check. копировать](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6), можно найти в центре сценариев. Ниже показан пример выходных данных этого сценария:

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
