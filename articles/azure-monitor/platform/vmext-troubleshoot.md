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
ms.openlocfilehash: 40f0705cfa7f0e9bb45d300a629adebd0cc5be47
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883679"
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
   * Можно также просмотреть файл журнала агента ВМ `C:\WindowsAzure\logs\WaAppAgent.log`
   * Если журнал не создан, значит агент виртуальной машины не установлен.
   * [Установка агента ВМ Azure](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Проверьте пульс расширения Microsoft Monitoring Agent, выполнив следующие действия:
   * Войдите на виртуальную машину.
   * Откройте планировщик задач и найдите задачу `update_azureoperationalinsight_agent_heartbeat`.
   * Убедитесь, что задача активна и выполняется ежеминутно.
   * Проверьте файл журнала пульса `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`
3. Просмотрите файлы журнала расширения ВМ Microsoft Monitoring Agent в `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
4. Убедитесь, что на виртуальной машине можно запускать сценарии PowerShell.
5. Убедитесь, что разрешения на доступ к папке C:\Windows\temp не были изменены.
6. Просмотреть состояние агента Microsoft Monitoring Agent, введя следующую команду в окне PowerShell с повышенными привилегиями на виртуальной машине `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
7. Просмотрите файлы журналов программы установки Microsoft Monitoring Agent в `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Подробные сведения см. в статье об [устранении неполадок расширений для виртуальных машин Windows](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Устранение неполадок расширения виртуальной машины для Linux
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Если расширение виртуальной машины *Агент Log Analytics для Linux* не устанавливается или не создает отчеты, можно выполнить следующие действия для решения проблемы.

1. Если состояние расширения *Неизвестный* проверьте, установлен ли агент виртуальной Машины Azure и работает правильно, просмотрев файл журнала агента ВМ `/var/log/waagent.log`
   * Если журнал не создан, значит агент виртуальной машины не установлен.
   * [Установите агент виртуальной Машины Azure на виртуальных машинах Linux](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Для возникновения других неполадок просмотрите агент Log Analytics для расширения виртуальной Машины Linux, файлы журналов `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` и `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Если состояние расширения находится в работоспособном состоянии, но данные не отправляются, просмотрите агент Log Analytics для файлов журнала Linux в `/var/opt/microsoft/omsagent/log/omsagent.log`

Подробные сведения см. в статье [Устранение неполадок расширения виртуальной машины Linux](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные рекомендации по устранению неполадок, связанных с агентом Log Analytics для Linux, размещенном на компьютерах за пределами Azure, см. в статье об [устранении неполадок агента Azure Log Analytics для Linux](agent-linux-troubleshoot.md).  
