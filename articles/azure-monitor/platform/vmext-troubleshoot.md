---
title: Устранение неполадок расширения виртуальной Машины Azure Log Analytics в Azure Monitor | Документация Майкрософт
description: Описание симптомов, причин и решений для наиболее распространенных проблем с расширением "Виртуальная машина Log Analytics" для виртуальных машин Windows и Linux в Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2018
ms.author: magoedte
ms.openlocfilehash: 17a52cf17c62c930dc3922ded48d659f56016688
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370505"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Устранение неполадок расширения виртуальной Машины Log Analytics в Azure Monitor
В этой статье содержатся сведения об устранении ошибок, которые могут возникнуть с расширением "Виртуальная машина Log Analytics" для виртуальных машин Windows и Linux, работающих на платформе Microsoft Azure, и предлагаются возможные решения для их устранения.

Чтобы проверить состояние расширения, выполните следующие действия на портале Azure.

1. Войдите на [портал Azure](https://portal.azure.com).
2. На портале Azure щелкните **Все службы**. В списке ресурсов введите **виртуальные машины**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Выберите **Виртуальные машины**.
3. В списке найдите и выберите виртуальную машину.
3. В колонке виртуальной машины щелкните **Расширения**.
4. Просмотрите список, чтобы проверить, включено ли расширение Log Analytics.  На виртуальных машинах Linux агент отображается как **OMSAgentforLinux**. На виртуальных машинах Windows он отображается как **MicrosoftMonitoringAgent**.

   ![Представление расширения виртуальной машины](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Выберите расширение, чтобы просмотреть сведения. 

   ![Сведения о расширении виртуальной машины](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Устранение неполадок расширения виртуальной машины Azure для Windows

Если расширение виртуальной машины *Microsoft Monitoring Agent* не устанавливается или не создает отчеты, можно выполнить следующие действия для решения проблемы.

1. Следуя инструкции в статье базы знаний [KB 2965986](https://support.microsoft.com/kb/2965986#mt1), убедитесь, что агент виртуальной машины установлен и работает правильно.
   * Также можно проверить, существует ли файл журнала агента ВМ `C:\WindowsAzure\logs\WaAppAgent.log`.
   * Если журнал не создан, значит агент виртуальной машины не установлен.
   * [Установка агента виртуальной машины Azure](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Проверьте пульс расширения Microsoft Monitoring Agent, выполнив следующие действия:
   * Войдите на виртуальную машину.
   * Откройте планировщик задач и найдите задачу `update_azureoperationalinsight_agent_heartbeat`.
   * Убедитесь, что задача активна и выполняется ежеминутно.
   * Проверьте, есть ли в папке `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log` файл журнала пульса.
3. Просмотрите файлы журнала расширения виртуальной машины Microsoft Monitoring Agent в папке `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`.
4. Убедитесь, что на виртуальной машине можно запускать сценарии PowerShell.
5. Убедитесь, что разрешения на доступ к папке C:\Windows\temp не были изменены.
6. Просмотрите состояние агента Microsoft Monitoring Agent. Для этого на виртуальной машине в окне PowerShell с повышенными правами введите следующий код `  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`.
7. Просмотрите файлы журнала установки агента Microsoft Monitoring Agent в папке `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`.

Подробные сведения см. в статье об [устранении неполадок расширений для виртуальных машин Windows](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Устранение неполадок расширения виртуальной машины для Linux
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Если расширение виртуальной машины *Агент Log Analytics для Linux* не устанавливается или не создает отчеты, можно выполнить следующие действия для решения проблемы.

1. Если состояние расширения *неизвестно*, убедитесь, что агент виртуальной машины Azure установлен и работает правильно, просмотрев файл журнала агента ВМ `/var/log/waagent.log`.
   * Если журнал не создан, значит агент виртуальной машины не установлен.
   * [Установите агент виртуальной машины Azure на виртуальной машине Linux](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension).
2. В случае возникновения других неполадок просмотрите файлы журнала расширения агента Log Analytics для Linux в папке `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` и `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`.
3. Если расширение работает нормально, но данные не отправляются, просмотрите файлы журнала агента Log Analytics для Linux в папке `/var/opt/microsoft/omsagent/log/omsagent.log`.

Подробные сведения см. в статье [Устранение неполадок расширения виртуальной машины Linux](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные рекомендации по устранению неполадок, связанных с агентом Log Analytics для Linux, размещенном на компьютерах за пределами Azure, см. в статье об [устранении неполадок агента Azure Log Analytics для Linux](agent-linux-troubleshoot.md).  
