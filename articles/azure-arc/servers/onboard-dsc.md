---
title: Установка агента подключенной машины с помощью Windows PowerShell DSC
description: В этой статье вы узнаете, как подключить машины к Azure с помощью Azure Arc для серверов (предварительного просмотра) с помощью Windows PowerShell DSC.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 1fb64463b0372202adb04c2deb304c389c7773b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164687"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Как установить агент Connected Machine с помощью Windows PowerShell DSC

С помощью [Windows PowerShell Желаемая конфигурация состояния](https://docs.microsoft.com/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) (DSC), вы можете автоматизировать установку программного обеспечения и конфигурацию для компьютера Windows. В этой статье описывается, как использовать DSC для установки Azure Arc для серверов connected Machine на гибридных машинах Windows.

## <a name="requirements"></a>Требования

- Версия 4.0 Windows PowerShell

- Модуль [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0) DSC

- Директор службы для подключения машин к Azure Arc для серверов, не интерактивных. Следуйте шагам в разделе [Создайте директора службы для посадки в масштабе,](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) если вы еще не создали принцип службы для Arc для серверов.

## <a name="install-the-connectedmachine-dsc-module"></a>Установка модуля ConnectedMachine DSC

1. Чтобы вручную установить модуль, загрузите исходный код и распакуйте содержимое каталога проекта в `$env:ProgramFiles\WindowsPowerShell\Modules folder`. Или запустите следующую команду для установки из галереи PowerShell с помощью PowerShellGet (в PowerShell 5.0):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. Чтобы подтвердить установку, запустите следующую команду и убедитесь, что вы видите доступные ресурсы DSC Azure Connected Machine.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   На выходе следует увидеть нечто похожее на следующее:

   ![Подтверждение примера установки модуля Connected Machine DSC](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Установите агент и подключитесь к Azure

Ресурсы в этом модуле предназначены для управления конфигурацией azure Connected Machine Agent. Также включен сценарий `AzureConnectedMachineAgent.ps1`PowerShell, `AzureConnectedMachineDsc\examples` найденный в папке. Он использует ресурсы сообщества для автоматизации загрузки и установки и установления связи с Azure Arc. Этот скрипт выполняет аналогичные действия, описанные в [гибридных машинах Connect к Azure из статьи портала Azure.](onboard-portal.md)

Если машине необходимо связаться через прокси-сервер с службой, после установки агента вам нужно запустить команду, описанную [здесь.](onboard-portal.md#configure-the-agent-proxy-setting) Она задает переменную системной среды для прокси-сервера `https_proxy`. Вместо того, чтобы выполнять команду вручную, вы можете выполнить этот шаг с DSC с помощью модуля [ComputeManagementDsc.](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0)

>[!NOTE]
>Чтобы позволить DSC работать, Windows должна быть настроена для получения удаленных команд PowerShell, даже если вы работаете с конфигурацией локального хоста. Чтобы правильно настроить среду, запустите `Set-WsManQuickConfig -Force` в терминале PowerShell с повышенными привилегиями.
>

Конфигурационные документы (файлы MOF) `Start-DscConfiguration` могут быть применены к машине с помощью cmdlet.

Ниже приведены параметры, которые вы переходят к скрипту PowerShell для использования.

- `TenantId`: Уникальный идентификатор (GUID), представляющий ваш специализированный экземпляр Azure AD.

- `SubscriptionId`: Идентификатор подписки (GUID) вашей подписки Azure, в которую вы хотите, чтобы в машинах.

- `ResourceGroup`: Имя группы ресурсов, где вы хотите, чтобы ваши подключенные машины принадлежали.

- `Location`: [См. поддерживаемые регионы Azure](overview.md#supported-regions). Это местоположение может быть таким же или разным, как местоположение группы ресурсов.

- `Tags`: Строка массив амтизм, который должен быть применен к подключенному ресурсу машины.

- `Credential`: Объект учетных данных PowerShell с **ApplicationId** и **паролем,** используемым для регистрации машин в масштабе с помощью [основного сервиса.](onboard-service-principal.md) 

1. В консоли PowerShell перейдите к папке, где вы сохранили `.ps1` файл.

2. Выполните следующие команды PowerShell для компиляции документа MOF (дополнительные сведения о компиляции конфигураций DSC см. в разделе [Конфигурации DSC](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations?view=powershell-7)).

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Это создаст `localhost.mof file` новую папку `C:\dsc`с именем .

После установки агента и настройки его подключения к Azure Arc для серверов (предварительная версия) перейдите на портал Azure, чтобы убедиться, что сервер подключен. Просмотрите свои компьютеры на [портале Azure](https://aka.ms/hybridmachineportal).

## <a name="adding-to-existing-configurations"></a>Добавление к существующим конфигурациям

Этот ресурс может быть добавлен к существующим конфигурациям DSC для представления сквозной конфигурации для машины. Например, можно добавить этот ресурс в конфигурацию, которая устанавливает безопасные настройки операционной системы.

Модуль [CompsiteResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0) из галереи PowerShell может быть использован для создания [композитного ресурса](https://docs.microsoft.com/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7) примерной конфигурации для дальнейшего упрощения сочетания конфигураций.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как управлять машиной с помощью [azure Policy,](../../governance/policy/overview.md)для таких вещей, как [конфигурация гостевых VM,](../../governance/policy/concepts/guest-configuration.md)проверка того, что машина отчитывается перед ожидаемым рабочим пространством Log Analytics, позволяет осуществлять мониторинг с помощью [Azure Monitor с помощью vMs](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)и многое другое.

- Подробнее об [агенте Log Analytics.](../../azure-monitor/platform/log-analytics-agent.md) Агент Log Analytics для Windows и Linux необходим, если требуется упреждающе отслеживать ОС и рабочие нагрузки на компьютере, выполнять управление с помощью runbook службы автоматизации или с помощью решений, таких как Управление обновлениями, или использовать другие службы Azure, например, [Центр безопасности Azure](../../security-center/security-center-intro.md).