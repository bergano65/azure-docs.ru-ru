---
title: Подключение гибридных компьютеров к Azure с помощью PowerShell
description: Из этой статьи вы узнаете, как установить агент и подключить компьютер к Azure с помощью серверов с поддержкой ARC в Azure с помощью PowerShell.
ms.date: 10/21/2020
ms.topic: conceptual
ms.openlocfilehash: d36fd174606b49b28b1d8343bff6ccc1f62e5194
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92376298"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>Подключение гибридных компьютеров к Azure с помощью PowerShell

Вы можете включить серверы с поддержкой ARC в Azure для одного или небольшого числа компьютеров Windows или Linux в вашей среде, выполнив набор действий вручную. Также можно использовать командлет PowerShell [Connect-азконнектедмачине](/powershell/module/az.connectedmachine/remove-azconnectedmachine). Этот командлет выполняет следующие действия:

- Настраивает на главном компьютере загрузку агента Windows из центра загрузки Майкрософт и пакета агента Linux из packages.microsoft.com.
- Устанавливает агент подключенного компьютера.
- Регистрация компьютера в службе "Дуга Azure"

Этот метод требует наличия разрешений администратора на компьютере для установки и настройки агента. В Linux, используя корневую учетную запись, и в Windows вы являетесь членом группы локальных администраторов.

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

## <a name="install-and-validate-the-agent-on-windows"></a>Установка и проверка агента в ОС Windows

1. Откройте консоль PowerShell с правами администратора.

2. Войдите в Azure, выполнив команду `Connect-AzAccount` .

3. Чтобы установить агент подключенного компьютера, используйте параметр `Connect-AzConnectedMachine` с `-Name` `-ResourceGroupName` параметрами, и `-Location` . Используйте `-SubscriptionId` параметр, чтобы переопределить подписку по умолчанию в результате создания контекста Azure после входа.

    Чтобы установить агент подключенного компьютера на целевом компьютере, который может напрямую взаимодействовать с Azure, выполните следующую команду:

    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
    ```
    
    Если целевой компьютер обменивается данными через прокси-сервер, выполните следующую команду:
    
    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
    ```

    Если не удается запустить агент после завершения установки, просмотрите подробные сведения об ошибке в журналах. В Windows по адресу *%програмдата%\азуреконнектедмачинеажент\лог\химдс.лог*и в Linux по адресу */Вар/ОПТ/азкмажент/лог/химдс.лог*.

## <a name="verify-the-connection-with-azure-arc"></a>Проверка подключения с помощью Azure Arc

После установки агента и настройки его подключения к серверам с поддержкой Azure Arc перейдите на портал Azure, чтобы убедиться, что сервер подключен. Просмотрите свои компьютеры на [портале Azure](https://portal.azure.com).

![Успешное соединение с сервером](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Дальнейшие действия

* Сведения об устранении неполадок можно найти в разделе [руководство по устранению неполадок подключенного компьютера](troubleshoot-agent-onboard.md).

* Узнайте, как управлять компьютером с помощью [Политики Azure](../../governance/policy/overview.md), например для [гостевой конфигурации](../../governance/policy/concepts/guest-configuration.md) виртуальной машины, проверять отправку отчетов с компьютера в ожидаемую рабочую область Log Analytics, включать мониторинг с помощью [Azure Monitor с виртуальными машинами](../../azure-monitor/insights/vminsights-enable-policy.md) и т. д.

* Узнайте больше об [агенте Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Агент Log Analytics для Windows и Linux необходим, если требуется получить данные мониторинга операционной системы и рабочей нагрузки, управлять ими с помощью модулей Runbook или функций автоматизации, таких как Управление обновлениями, или использовать другие службы Azure, такие как [Центр безопасности Azure](../../security-center/security-center-introduction.md).