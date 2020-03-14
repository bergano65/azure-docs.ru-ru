---
title: Установка агента подключенного компьютера с помощью Windows PowerShell DSC
description: Из этой статьи вы узнаете, как подключить компьютеры к Azure с помощью дуги Azure для серверов (Предварительная версия) с помощью Windows PowerShell DSC.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 1fb64463b0372202adb04c2deb304c389c7773b8
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79164687"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Установка агента подключенного компьютера с помощью Windows PowerShell DSC

С помощью [настройки требуемого состояния Windows PowerShell](https://docs.microsoft.com/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) (DSC) можно автоматизировать установку и настройку программного обеспечения для компьютера Windows. В этой статье описывается, как с помощью DSC установить дугу Azure для подключенных к серверам агентов компьютера на гибридных компьютерах Windows.

## <a name="requirements"></a>Требования

- Windows PowerShell версии 4,0 или более поздней

- Модуль DSC [азуреконнектедмачинедск](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0)

- Субъект-служба для подключения компьютеров к службе "Дуга Azure" для серверов в неинтерактивном режиме. Выполните действия, описанные в разделе [Создание субъекта-службы для адаптации в масштабе](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) , если вы еще не создали субъект-службу для серверов ARC.

## <a name="install-the-connectedmachine-dsc-module"></a>Установка модуля DSC Коннектедмачине

1. Чтобы вручную установить модуль, скачайте исходный код и распакуйте содержимое каталога проекта на `$env:ProgramFiles\WindowsPowerShell\Modules folder`. Или выполните следующую команду, чтобы установить из коллекции PowerShell с помощью PowerShellGet (в PowerShell 5,0):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. Чтобы подтвердить установку, выполните следующую команду и убедитесь, что вы видите доступные ресурсы DSC для подключенных компьютеров Azure.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   В выходных данных вы должны увидеть примерно следующее:

   ![Пример подтверждения установки модуля DSC подключенного компьютера](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Установка агента и подключение к Azure

Ресурсы в этом модуле предназначены для управления конфигурацией агента подключенного компьютера Azure. Также включен сценарий PowerShell `AzureConnectedMachineAgent.ps1`, который находится в папке `AzureConnectedMachineDsc\examples`. Он использует ресурсы сообщества для автоматизации загрузки и установки и установления подключения к службе "Дуга Azure". Этот сценарий выполняет аналогичные действия, описанные в статье [портал Azure подключение гибридных компьютеров к Azure](onboard-portal.md) .

Если компьютеру необходимо взаимодействовать через прокси-сервер со службой, после установки агента необходимо выполнить команду, описанную [здесь](onboard-portal.md#configure-the-agent-proxy-setting). Она задает переменную системной среды для прокси-сервера `https_proxy`. Вместо выполнения команды вручную можно выполнить этот шаг с DSC с помощью модуля [компутеманажементдск](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0) .

>[!NOTE]
>Чтобы разрешить выполнение DSC, Windows необходимо настроить на получение удаленных команд PowerShell, даже если используется конфигурация localhost. Чтобы правильно настроить среду, запустите `Set-WsManQuickConfig -Force` в терминале PowerShell с повышенными привилегиями.
>

Документы конфигурации (MOF-файлы) можно применить к компьютеру с помощью командлета `Start-DscConfiguration`.

Ниже приведены параметры, которые передаются в скрипт PowerShell для использования.

- `TenantId`: уникальный идентификатор (GUID), представляющий выделенный экземпляр Azure AD.

- `SubscriptionId`: идентификатор подписки (GUID) подписки Azure, в которой должны быть компьютеры.

- `ResourceGroup`: имя группы ресурсов, к которой должны принадлежать подключенные компьютеры.

- `Location`: см. раздел [Поддерживаемые регионы Azure](overview.md#supported-regions). Это расположение может совпадать с расположением группы ресурсов или совпадать с ним.

- `Tags`: строковый массив тегов, которые должны быть применены к ресурсу подключенного компьютера.

- `Credential`: объект учетных данных PowerShell с идентификатором **applicationId** и **Password** , используемый для регистрации компьютеров в масштабе с помощью [субъекта-службы](onboard-service-principal.md). 

1. В консоли PowerShell перейдите к папке, в которую был сохранен файл `.ps1`.

2. Выполните следующие команды PowerShell для компиляции документа MOF (дополнительные сведения о компиляции конфигураций DSC см. в разделе [Конфигурации DSC](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations?view=powershell-7)).

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Это приведет к созданию `localhost.mof file` в новой папке с именем `C:\dsc`.

После установки агента и настройки его подключения к Azure Arc для серверов (предварительная версия) перейдите на портал Azure, чтобы убедиться, что сервер подключен. Просмотрите свои компьютеры на [портале Azure](https://aka.ms/hybridmachineportal).

## <a name="adding-to-existing-configurations"></a>Добавление в существующие конфигурации

Этот ресурс можно добавить в существующие конфигурации DSC, чтобы представить комплексную конфигурацию для компьютера. Например, может потребоваться добавить этот ресурс в конфигурацию, которая задает параметры безопасности операционной системы.

Модуль [компситересаурце](https://www.powershellgallery.com/packages/compositeresource/0.4.0) из коллекция PowerShell можно использовать для создания [составного ресурса](https://docs.microsoft.com/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7) в примере конфигурации для дальнейшего упрощения объединения конфигураций.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как управлять компьютером с помощью [политики Azure](../../governance/policy/overview.md), например [гостевой конфигурации](../../governance/policy/concepts/guest-configuration.md)виртуальной машины, проверки того, что компьютер сообщает о предполагаемой log Analytics рабочей области, включить мониторинг с помощью [Azure Monitor с виртуальными машинами](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)и многое другое.

- Дополнительные сведения об [агенте log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Агент Log Analytics для Windows и Linux необходим, если требуется упреждающе отслеживать ОС и рабочие нагрузки на компьютере, выполнять управление с помощью runbook службы автоматизации или с помощью решений, таких как Управление обновлениями, или использовать другие службы Azure, например, [Центр безопасности Azure](../../security-center/security-center-intro.md).