---
title: Подключение гибридных компьютеров к Azure в большом масштабе
description: В этой статье вы узнаете, как подключить компьютеры к Azure с помощью Azure Arc для серверов (предварительная версия), используя субъект-службу.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: ac6a00efa7db848e4c05703c81ba835fbf5f77e3
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86103795"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Подключение гибридных компьютеров к Azure в большом масштабе

Вы можете использовать Azure Arc для серверов (предварительная версия) для нескольких компьютеров Windows или Linux в своей среде. При этом доступно несколько гибких возможностей в зависимости от ваших требований. Используя предоставленный сценарий шаблона, можно автоматизировать каждый шаг установки, включая установку подключения к Azure Arc. Тем не менее, вам потребуется интерактивно выполнить этот сценарий с учетной записью, обладающей повышенным уровнем разрешений на целевом компьютере и в Azure. Чтобы подключить компьютеры к Azure Arc для серверов, вместо привилегированного удостоверения для [интерактивного подключения к компьютеру](onboard-portal.md) можно использовать [субъект-службу](../../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory. Субъект-служба — это специальное ограниченное удостоверение управления, которому предоставляются только минимальные разрешения, необходимые для подключения компьютеров к Azure с помощью команды `azcmagent`. Это более безопасно, чем использование учетной записи с более высоким уровнем привилегий, такой как администратор клиента, и соответствует рекомендациям по обеспечению безопасности управления доступом. Субъект-служба используется только во время подключения и не используется для других целей.  

Методы установки и настройки агента Connected Machine требуют, чтобы для автоматизированного метода на компьютерах вам были предоставлены разрешения администратора. В Linux для этого используется корневая учетная запись, а в Windows следует быть членом группы локальных администраторов.

Прежде чем приступить к работе, ознакомьтесь с данными о [необходимых компонентах](agent-overview.md#prerequisites) и убедитесь, что подписка и ресурсы соответствуют требованиям.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

По завершении этого процесса ваши гибридные компьютеры будут успешно подключены к Azure Arc для серверов.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Создание субъекта-службы для подключения в большом масштабе

Вы можете создать субъект-службу с помощью [Azure PowerShell](/powershell/azure/install-az-ps), выполнив командлет [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal). Для этого можно также выполнить действия, описанные в статье [Создание субъекта-службы с помощью портала Azure](../../active-directory/develop/howto-create-service-principal-portal.md).

> [!NOTE]
> При создании субъекта-службы ваша учетная запись должна быть владельцем или администратором доступа пользователей в подписке, которую вы хотите использовать для подключения. Если у вас нет достаточных разрешений для создания назначений ролей, вы сможете создать субъект-службу, но вам не удастся подключить компьютеры.
>

Чтобы создать субъект-службу в PowerShell, сделайте следующее.

1. Выполните следующую команду. Необходимо сохранить выходные данные командлета [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) в переменной, иначе вы не сможете получить пароль, который потребуется позже.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. Чтобы получить пароль, хранящийся в переменной `$sp`, выполните следующую команду.

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. В выходных данных найдите значение пароля в поле **password** и скопируйте его. Кроме того, найдите значение в поле **ApplicationId** и скопируйте его. Сохраните их в безопасном месте для последующего использования. Если вы забудете или потеряете пароль имени субъекта-службы, его можно сбросить с помощью командлета [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential).

Значения из следующих свойств используются для параметров, передаваемыми в `azcmagent`.

* Свойство **ApplicationId** используется в качестве значения параметра `--service-principal-id`.
* Свойство **password** используется в качестве значения `--service-principal-secret` при подключении агента.

> [!NOTE]
> Убедитесь, что для субъекта-службы используется свойство **ApplicationId**, а не **Id**.
>

Роль **Подключение Azure Connected Machine** содержит только разрешения, необходимые для подключения компьютера. Вы можете назначить субъекту-службе разрешение, чтобы добавить в его область группу ресурсов или подписку. Чтобы добавить назначение ролей, ознакомьтесь с разделом [Добавление или удаление назначений ролей Azure с помощью портала Azure](../../role-based-access-control/role-assignments-portal.md) или [Добавление или удаление назначений ролей Azure с помощью Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="install-the-agent-and-connect-to-azure"></a>Установка агента и подключение к Azure

Ниже описывается установка и настройка агента Connected Machine на гибридных компьютерах с помощью шаблона сценария, который выполняет действия, аналогичные описанным в статье [Подключение гибридных компьютеров к Azure на портале Azure](onboard-portal.md). Разница заключается в последнем шаге установки подключения к Azure Arc с помощью команды `azcmagent` и субъекта-службы. 

Ниже приведены параметры команды `azcmagent` для использования субъекта-службы.

* `tenant-id` : Уникальный идентификатор (GUID), представляющий выделенный экземпляр Azure AD.
* `subscription-id` : Идентификатор (GUID) подписки Azure, в которой должны размещаться компьютеры.
* `resource-group` : Имя группы ресурсов, к которой должны принадлежать подключенные компьютеры.
* `location` : Ознакомьтесь с [поддерживаемыми регионами Azure](overview.md#supported-regions). Это расположение может совпадать или не совпадать с расположением группы ресурсов.
* `resource-name` : (*Необязательно*.) Используется для представления ресурсов Azure на локальном компьютере. Если это значение не указано, будет использовано имя узла компьютера.

Вы можете узнать больше о программе командной строки `azcmagent`, изучив [справочные материалы по Azcmagent](./manage-agent.md).

### <a name="windows-installation-script"></a>Сценарий установки для Windows

Ниже приведен пример сценария установки агента Connected Machine для Windows, который был изменен, чтобы использовать субъект-службу для поддержки полностью автоматизированной, неинтерактивной установки агента.

```
 # Download the package
function download() {$ProgressPreference="SilentlyContinue"; Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi}
download

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String

# Run connect command
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
```

### <a name="linux-installation-script"></a>Сценарий установки для Linux

Ниже приведен пример сценария установки агента Connected Machine для Linux, который был изменен, чтобы использовать субъект-службу для поддержки полностью автоматизированной, неинтерактивной установки агента.

```
# Download the installation package
wget https://aka.ms/azcmagent -O ~/install_linux_azcmagent.sh

# Install the hybrid agent
bash ~/install_linux_azcmagent.sh

# Run connect command
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
```

После установки агента и настройки его подключения к Azure Arc для серверов (предварительная версия) перейдите на портал Azure, чтобы убедиться, что сервер подключен. Просмотрите свои компьютеры на [портале Azure](https://aka.ms/hybridmachineportal).

![Успешное соединение с сервером](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как управлять компьютером с помощью [Политики Azure](../../governance/policy/overview.md), например, для [гостевой конфигурации](../../governance/policy/concepts/guest-configuration.md) виртуальной машины, проверять отправку отчетов с компьютера в ожидаемую рабочую область Log Analytics, включать мониторинг с помощью [Azure Monitor с виртуальными машинами](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md) и многое другое.

- Узнайте больше об [агенте Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Агент Log Analytics для Windows и Linux необходим, если требуется упреждающе отслеживать ОС и рабочие нагрузки на компьютере, выполнять управление с помощью runbook службы автоматизации или с помощью решений, таких как Управление обновлениями, или использовать другие службы Azure, например, [Центр безопасности Azure](../../security-center/security-center-intro.md).
