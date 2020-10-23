---
title: Развертывание гибридной рабочей роли Runbook в службе автоматизации Linux
description: В этой статье рассказывается, как установить гибридную рабочую роль Runbook службы автоматизации Azure для запуска модулей Runbook на компьютерах под управлением Linux в локальном центре обработки данных или в облачной среде.
services: automation
ms.subservice: process-automation
ms.date: 10/06/2020
ms.topic: conceptual
ms.openlocfilehash: c84f168104be4ba4cb8af2e31be82eed0e2ae83a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205190"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Развертывание гибридной рабочей роли Runbook для Linux

Вы можете использовать функцию гибридной рабочей роли Runbook в службе автоматизации Azure для запуска модулей Runbook непосредственно на компьютере, на котором размещается роль, и для ресурсов в среде, чтобы управлять этими локальными ресурсами. Гибридная рабочая роль Runbook Linux запускает модули runbook от имени особого пользователя, для которого можно повысить разрешения, чтобы выполнить команды, требующие повышения разрешений. Служба автоматизации Azure хранит и управляет модулями Runbook, а затем доставляет их на один или несколько определенных компьютеров. В этой статье описывается, как установить гибридную рабочую роль Runbook на компьютере Linux, как удалить отдельную рабочую роль и их группу.

После успешного развертывания рабочей роли Runbook ознакомьтесь с [запуском модулей runbook в гибридной рабочей роли Runbook](automation-hrw-run-runbooks.md), чтобы узнать, как настроить модули runbook для автоматизации процессов в локальном центре обработки данных или другой облачной среде.

## <a name="prerequisites"></a>предварительные требования

Чтобы начать, у вас должны быть следующие компоненты:

### <a name="a-log-analytics-workspace"></a>Рабочая область Log Analytics

Гибридная Рабочая роль Runbook зависит от Azure Monitor Log Analytics рабочей области для установки и настройки роли. Его можно создать с помощью [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), с помощью [PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)или в [портал Azure](../azure-monitor/learn/quick-create-workspace.md).

Если у вас нет рабочей области Log Analytics Azure Monitor, ознакомьтесь с [руководством по проектированию журналов Azure Monitor](../azure-monitor/platform/design-logs-deployment.md) перед созданием рабочей области.

Если у вас есть рабочая область, но она не связана с учетной записью службы автоматизации, включение функции автоматизации расширяет возможности службы автоматизации Azure, включая поддержку гибридной рабочей роли Runbook. При включении одной из функций службы автоматизации Azure в рабочей области Log Analytics, в частности [Управление обновлениями](update-management/update-mgmt-overview.md) или [Отслеживание изменений и инвентаризации](change-tracking/overview.md), рабочие компоненты автоматически отправляются на компьютер агента.

Чтобы добавить компонент Управление обновлениями в рабочую область, выполните следующий командлет PowerShell:

```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

Чтобы добавить функцию Отслеживание изменений и инвентаризации в рабочую область, выполните следующий командлет PowerShell:

```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Агент Log Analytics

Для работы гибридной рабочей роли Runbook требуется [агент log Analytics](../azure-monitor/platform/log-analytics-agent.md) для поддерживаемой операционной системы Linux.

>[!NOTE]
>После установки агента Log Analytics для Linux не следует изменять разрешения `sudoers.d` папки или ее владельца. Для учетной записи **нксаутоматион** требуется разрешение sudo, то есть пользовательский контекст, в котором выполняется Гибридная Рабочая роль Runbook. Не следует удалять разрешения. Это может привести к критическим изменениям в определенных папках или командах.
>

### <a name="supported-linux-operating-systems"></a>Поддерживаемые операционные системы Linux

Дистрибутивы, поддерживаемые функцией гибридной рабочей роли Runbook:

* Amazon Linux 2012.09–2015.09 (x86/x64)
* CentOS Linux 5, 6 и 7 (x86/x64)
* Oracle Linux 5, 6 и 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 и 7 (x86/x64)
* Debian GNU/Linux 6, 7 и 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS и 18.04 (x86/x64)
* SUSE Linux Enterprise Server 12 (x86 и x64)

### <a name="minimum-requirements"></a>Минимальные требования

Минимальные требования для гибридной рабочей роли Runbook Linux:

* два ядра;
* 4 ГБ ОЗУ;
* Порт 443 (исходящий).

| **Требуемый пакет** | **Описание** | **Минимальная версия**|
|--------------------- | --------------------- | -------------------|
|Glibc |Библиотека C GNU| 2.5-12 |
|Openssl| Библиотеки OpenSSL | 1.0 (поддерживается TLS 1.1 и TLS 1.2)|
|Curl | Веб-клиент cURL | 7.15.5|
|Python-ctypes | Требуется компонент Python 2.x |
|PAM | Подключаемые модули аутентификации|
| **Дополнительный пакет** | **Описание** | **Минимальная версия**|
| PowerShell Core | Для запуска модулей Runbook PowerShell необходимо установить PowerShell Core. Подробные сведения об установке см. в статье [Установка PowerShell Core в Linux](/powershell/scripting/install/installing-powershell-core-on-linux). | 6.0.0 |

## <a name="supported-linux-hardening"></a>Поддерживаемые усиление защиты Linux

Следующие еще не поддерживаются:

* СОСТОЯНИИ

## <a name="supported-runbook-types"></a>Поддерживаемые типы runbook

Гибридные рабочие роли Runbook Linux поддерживают ограниченный набор типов Runbook в службе автоматизации Azure, и они описаны в следующей таблице.

|Тип Runbook | Поддерживается |
|-------------|-----------|
|Python 2; |Да |
|PowerShell |Да<sup>1</sup> |
|Рабочий процесс PowerShell |Нет |
|Графический |Нет |
|графический модуль рабочего процесса PowerShell. |Нет |

<sup>1</sup> Для модулей Runbook PowerShell необходимо установить PowerShell Core на компьютере Linux. Подробные сведения об установке см. в статье [Установка PowerShell Core в Linux](/powershell/scripting/install/installing-powershell-core-on-linux).

## <a name="install-a-linux-hybrid-runbook-worker"></a>Установка гибридной рабочей роли Runbook для Linux

Чтобы установить и настроить гибридную рабочую роль Runbook Linux, выполните следующие действия.

1. Разверните агент Log Analytics на целевом компьютере.

    * Для виртуальных машин Azure установите агент Log Analytics для Linux с помощью [расширения виртуальной машины для Linux](../virtual-machines/extensions/oms-linux.md). Расширение устанавливает агент Log Analytics на виртуальных машинах Azure и регистрирует виртуальные машины в существующей Log Analytics рабочей области с помощью шаблона Azure Resource Manager или Azure CLI. После установки агента виртуальную машину можно добавить в группу гибридных рабочих ролей Runbook в учетной записи службы автоматизации.

    * Для виртуальных машин, не относящихся к Azure, установите агент Log Analytics для Linux с помощью параметров развертывания, описанных в статье [Подключение компьютеров Linux к Azure Monitor](../azure-monitor/platform/agent-linux.md) . Этот процесс можно повторить для нескольких компьютеров, чтобы добавить в среду несколько рабочих ролей. После установки агента виртуальные машины можно добавить в группу гибридных рабочих ролей Runbook в учетной записи службы автоматизации.

    > [!NOTE]
    > Для управления конфигурацией компьютеров, поддерживающих гибридную рабочую роль Runbook с настройкой требуемого состояния (DSC), необходимо добавить компьютеры в качестве узлов DSC.

    > [!NOTE]
    > Во время установки гибридной рабочей роли Linux должна присутствовать учетная запись [nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) с соответствующими разрешениями для работы команды sudo. Если вы попытаетесь установить рабочую роль, а учетная запись отсутствует или не имеет соответствующих разрешений, установка завершится сбоем.

2. Убедитесь, что агент сообщает о рабочей области.

    Агент Log Analytics для Linux подключает компьютеры к рабочей области Log Analytics Azure Monitor. При установке агента на компьютере и подключении его к рабочей области автоматически загружаются компоненты, необходимые для работы гибридного рабочего процесса Runbook.

    Подключение агента к рабочей области Log Analytics занимает несколько минут, и после этого вы можете выполнить следующий запрос и убедиться, что он отправляет данные пульса в рабочую область.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    В результатах поиска должны отобразиться записи пульса для компьютера, указывающие, что он подключен и сообщает службе. По умолчанию каждый агент перенаправляет запись пульса в назначенную ему рабочую область.

3. Выполните следующую команду, чтобы добавить компьютер в группу гибридной рабочей роли Runbook, указав значения параметров `-w` ,, `-k` `-g` и `-e` .

    Сведения, необходимые для параметров, можно получить `-k` на `-e` странице **ключи** в учетной записи службы автоматизации. Выберите **ключи** в разделе **Параметры учетной записи** в левой части страницы.

    ![Страница "Управление ключами"](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Для `-e` параметра скопируйте значение для **URL-адреса**.

    * Для `-k` параметра скопируйте значение для **первичного ключа доступа**.

    * В качестве `-g` параметра укажите имя группы гибридных рабочих ролей Runbook, к которой будет присоединена Новая Гибридная Рабочая роль Runbook Linux. Если эта группа уже существует в учетной записи службы автоматизации, в нее добавляется текущий компьютер. Если эта группа не существует, она будет создана с таким именем.

    * В качестве `-w` параметра укажите идентификатор рабочей области log Analytics.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

4. После выполнения команды на странице группы гибридных рабочих ролей в учетной записи службы автоматизации отобразится новая группа и число членов. Если указать имеющуюся группу, количество элементов в ней увеличивается. Вы можете выбрать группу из списка на странице "Группы гибридных рабочих ролей", а затем выбрать плитку **Гибридные рабочие роли**. На странице "Гибридные рабочие роли" отображается список элементов группы.

    > [!NOTE]
    > Если вы используете Log Analytics расширение виртуальной машины для Linux для виртуальной машины Azure, мы рекомендуем установить `autoUpgradeMinorVersion` в `false` качестве версий автоматического обновления, которые могут вызвать проблемы с гибридной рабочей ролью Runbook. Сведения об обновлении расширения вручную см. в разделе [Развертывание с помощью Azure CLI](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>Отключение проверки подписи

По умолчанию для гибридных рабочих ролей Runbook Linux требуется проверка подписи. При запуске неподписанной последовательности runbook для рабочей роли отображается ошибка `Signature validation failed`. Чтобы отключить проверку подписи, выполните следующую команду. Замените второй параметр идентификатором вашей рабочей области Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-machine"></a><a name="remove-linux-hybrid-runbook-worker"></a>Удаление гибридной рабочей роли Runbook с локального компьютера Linux

Вы можете использовать команду `ls /var/opt/microsoft/omsagent` в гибридной рабочей роли Runbook, чтобы получить идентификатор рабочей области. При этом создается папка с именем, соответствующим идентификатору рабочей области.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Этот сценарий не удаляет агент Log Analytics для Linux с компьютера. Он удаляет только функциональные возможности и конфигурацию гибридной рабочей роли Runbook.

## <a name="remove-a-hybrid-worker-group"></a>Удаление группы гибридных рабочих ролей

Чтобы удалить группу гибридных рабочих ролей Runbook на компьютерах Linux, используйте те же действия, что и для группы гибридных рабочих ролей Windows. См. раздел [Удаление группы гибридных рабочих ролей](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать, как настроить модули runbook для автоматизации процессов в локальном центре обработки данных или другой облачной среде, см. статью [Запуск модулей runbook в гибридной рабочей роли Runbook](automation-hrw-run-runbooks.md).

* Дополнительные сведения см. в разделе "Linux" статьи [Устранение неполадок с гибридными рабочими ролями Runbook](troubleshoot/hybrid-runbook-worker.md#linux).
