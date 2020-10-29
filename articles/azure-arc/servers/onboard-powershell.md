---
title: Подключение гибридных компьютеров к Azure с помощью PowerShell
description: Из этой статьи вы узнаете, как установить агент и подключить компьютер к Azure с помощью серверов с поддержкой ARC в Azure с помощью PowerShell.
ms.date: 10/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0755846ef02377edade98b69e478908a111ab247
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92901535"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>Подключение гибридных компьютеров к Azure с помощью PowerShell

Вы можете включить серверы с поддержкой ARC в Azure для одной или нескольких компьютеров Windows или Linux в своей среде, выполнив набор действий вручную. Также можно использовать командлет PowerShell [Connect-азконнектедмачине](/powershell/module/az.connectedmachine/remove-azconnectedmachine) , чтобы скачать агент подключенного компьютера, установить агент и зарегистрировать машину в службе "Дуга Azure". Командлет загружает пакет установщик Windows агента Windows из центра загрузки Майкрософт и пакет агента Linux из репозитория пакетов Майкрософт.

Этот метод требует наличия разрешений администратора на компьютере для установки и настройки агента. В Linux, используя корневую учетную запись, и в Windows вы являетесь членом группы локальных администраторов. Этот процесс можно выполнить в интерактивном или удаленном режиме на сервере Windows Server с помощью [удаленного взаимодействия PowerShell](/powershell/scripting/learn/ps101/08-powershell-remoting).

Прежде чем приступить к работе, ознакомьтесь с данными о [необходимых компонентах](agent-overview.md#prerequisites) и убедитесь, что подписка и ресурсы соответствуют требованиям. Сведения о поддерживаемых регионах и других связанных вопросах см. в разделе [Поддерживаемые регионы Azure](overview.md#supported-regions).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

- Компьютер с Azure PowerShell. Инструкции см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/).

Прежде чем использовать Azure PowerShell для управления расширениями виртуальной машины на гибридном сервере, управляемом серверами с поддержкой Arc, необходимо установить `Az.ConnectedMachine` модуль. Выполните следующую команду на сервере с поддержкой ARC:

```powershell
Install-Module -Name Az.ConnectedMachine
```

После завершения установки возвращается следующее сообщение:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Установка агента и подключение к Azure

1. Откройте консоль PowerShell с повышенными привилегиями.

2. Войдите в Azure, выполнив команду `Connect-AzAccount` .

3. Чтобы установить агент подключенного компьютера, используйте параметр `Connect-AzConnectedMachine` с `-Name` `-ResourceGroupName` параметрами, и `-Location` . Используйте `-SubscriptionId` параметр, чтобы переопределить подписку по умолчанию в результате создания контекста Azure после входа. Выполните одну из следующих команд:

    * Чтобы установить агент подключенного компьютера на целевом компьютере, который может напрямую взаимодействовать с Azure, выполните:

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
        ```
    
    * Чтобы установить агент подключенного компьютера на целевом компьютере, который обменивается данными через прокси-сервер, выполните:
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
        ```

Если не удается запустить агент после завершения установки, просмотрите подробные сведения об ошибке в журналах. В Windows по адресу *%програмдата%\азуреконнектедмачинеажент\лог\химдс.лог* и в Linux по адресу */Вар/ОПТ/азкмажент/лог/химдс.лог* .

## <a name="install-and-connect-using-powershell-remoting"></a>Установка и подключение с помощью удаленного взаимодействия PowerShell

Выполните следующие действия, чтобы настроить один или несколько серверов Windows с серверами с поддержкой дуги Azure. На удаленном компьютере должна быть включена служба удаленного взаимодействия PowerShell. Это можно сделать с помощью командлета `Enable-PSRemoting`.

1. Откройте консоль PowerShell с правами администратора.

2. Войдите в Azure, выполнив команду `Connect-AzAccount` .

3. Чтобы установить агент подключенного компьютера, используйте параметр `Connect-AzConnectedMachine` с `-Name` `-ResourceGroupName` параметрами, и `-Location` . Используйте `-SubscriptionId` параметр, чтобы переопределить подписку по умолчанию в результате создания контекста Azure после входа.

    * Чтобы установить агент подключенного компьютера на целевом компьютере, который может напрямую взаимодействовать с Azure, выполните следующую команду:
    
        ```azurepowershell
        $session = Connect-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -PSSession $session
        ```
    
    * Чтобы установить агент подключенного компьютера одновременно на нескольких удаленных компьютерах, добавьте список имен удаленных компьютеров, разделенных запятыми.

        ```azurepowershell
        $session = Connect-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -PSSession $session
        ```

    В следующем примере приведены результаты выполнения команды, предназначенной для одного компьютера:
    
    ```azurepowershell
    time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
    time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
    time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
    time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941
    
    Name           Location OSName   Status     ProvisioningState
    ----           -------- ------   ------     -----------------
    myMachineName  eastus   windows  Connected  Succeeded
    ```

## <a name="verify-the-connection-with-azure-arc"></a>Проверка подключения с помощью Azure Arc

После установки агента и настройки его подключения к серверам с поддержкой Azure Arc перейдите на портал Azure, чтобы убедиться, что сервер подключен. Просмотрите свои компьютеры на [портале Azure](https://portal.azure.com).

![Успешное соединение с сервером](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Дальнейшие действия

* Сведения об устранении неполадок можно найти в разделе [руководство по устранению неполадок подключенного компьютера](troubleshoot-agent-onboard.md).

* Узнайте, как управлять компьютером с помощью [Политики Azure](../../governance/policy/overview.md), например для [гостевой конфигурации](../../governance/policy/concepts/guest-configuration.md) виртуальной машины, проверять отправку отчетов с компьютера в ожидаемую рабочую область Log Analytics, включать мониторинг с помощью [Azure Monitor с виртуальными машинами](../../azure-monitor/insights/vminsights-enable-policy.md) и т. д.

* Узнайте больше об [агенте Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Агент Log Analytics для Windows и Linux необходим, если требуется получить данные мониторинга операционной системы и рабочей нагрузки, управлять ими с помощью модулей Runbook или функций автоматизации, таких как Управление обновлениями, или использовать другие службы Azure, такие как [Центр безопасности Azure](../../security-center/security-center-introduction.md).