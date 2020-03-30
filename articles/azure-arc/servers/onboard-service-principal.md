---
title: Подключение гибридных машин к Azure в масштабе
description: В этой статье вы узнаете, как подключить машины к Azure с помощью Azure Arc для серверов (предварительного просмотра) с помощью принципа службы.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 3a19dc019d2566ddddb2c0ba7988b342d30a45d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77192276"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Подключение гибридных машин к Azure в масштабе

Вы можете включить Azure Arc для серверов (предварительный просмотр) для нескольких компьютеров Windows или Linux в вашей среде с несколькими гибкими вариантами в зависимости от ваших требований. Используя применяемый нам шаблон, можно автоматизировать каждый этап установки, включая установление соединения с Azure Arc. Однако требуется интерактивно выполнить этот скрипт с помощью учетной записи с повышенными разрешениями на целевой машине и в Azure. Для подключения машин к Azure Arc для серверов можно использовать [принцип службы](../../active-directory/develop/app-objects-and-service-principals.md) Active Directory Azure вместо того, чтобы использовать привилегированное удостоверение личности для [интерактивного подключения машины.](onboard-portal.md) Директор службы — это специальная ограниченная иденция управления, которая `azcmagent` получает только минимальное разрешение, необходимое для подключения машин к Azure с помощью команды. Это безопаснее, чем использование более привилегировой учетной записи, такой как Администратор арендаторов, и соответствует нашим рекомендациям по обеспечению безопасности доступа. Основной сервис используется только во время посадки на борт, он не используется для каких-либо других целей.  

Методы установки для установки и настройки агента Connected Machine требуют, чтобы используемый автоматический метод имел разрешения администратора на машинах. На Linux, используя корневую учетную запись и на Windows, в качестве члена группы местных администраторов.

Прежде чем приступить к работе, ознакомьтесь с данными о [необходимых компонентах](overview.md#prerequisites) и убедитесь, что подписка и ресурсы соответствуют требованиям.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

В конце этого процесса вы успешно подключите гибридные машины к Azure Arc для серверов.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Создание директора службы для посадки в масштабе

Вы можете использовать [Azure PowerShell](/powershell/azure/install-az-ps) для создания директора службы с [помощью cmdlet New-AzADServicePrincipal.](/powershell/module/Az.Resources/New-AzADServicePrincipal) Или вы можете следовать шагам, перечисленным в [книге «Создание принципала службы», используя портал Azure](../../active-directory/develop/howto-create-service-principal-portal.md) для выполнения этой задачи.

> [!NOTE]
> При создании основного сервиса ваша учетная запись должна быть владельцем или администратором пользовательского доступа в подписке, которую вы хотите использовать для посадки. Если у вас нет достаточных разрешений для создания назначений ролей, вы сможете создать субъект-службу, но вам не удастся подключить компьютеры.
>

Чтобы создать основную службу с помощью PowerShell, выполните следующее.

1. Выполните следующую команду. Вы должны хранить выход [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) cmdlet в переменной, иначе вы не сможете получить пароль, необходимый на более позднем этапе.

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

2. Чтобы получить пароль, хранящийся в `$sp` переменной, запустите следующую команду:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. На выходе найдите значение пароля под **полем и** скопируйте его. Также найдите значение под полем **ApplicationId** и скопируйте его также. Сохраните их на потом в безопасном месте. Если вы забыли или потеряли основной пароль службы, вы можете сбросить его с помощью [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) cmdlet.

Значения из следующих свойств используются с параметрами, передаваемыми к `azcmagent`:

* Значение из свойства **ApplicationId** используется `--service-principal-id` для значения параметра
* Значение из свойства **пароля** используется `--service-principal-secret` для параметра, используемого для подключения агента.

> [!NOTE]
> Убедитесь в том, чтобы использовать основное свойство **ApplicationId,** а не свойство **Id.**
>

Роль **onboarding Azure Connected Machine** содержит только разрешения, необходимые для посадки на борт машины. Можно назначить главному разрешению службы, чтобы включить ее область в нее группа ресурсов или подписку. Чтобы добавить назначение ролей, смотрите [Добавить или удалять назначения ролей с помощью Azure RBAC и портала Azure](../../role-based-access-control/role-assignments-portal.md) или [добавить или удалить назначения ролей с помощью Azure RBAC и Azure CLI.](../../role-based-access-control/role-assignments-cli.md)

## <a name="install-the-agent-and-connect-to-azure"></a>Установите агент и подключитесь к Azure

Следующие шаги устанавливают и настраивают агент Connected Machine на гибридных машинах с помощью шаблона скрипта, который выполняет аналогичные шаги, описанные в [гибридных машинах Connect, в Azure из статьи портала Azure.](onboard-portal.md) Разница заключается в заключительном этапе, когда вы `azcmagent` устанавливаете подключение к Azure Arc с помощью команды с помощью основного обслуживания. 

Ниже приведены параметры, которые вы `azcmagent` настраиваете команду для использования для основного обслуживания.

* `tenant-id`: Уникальный идентификатор (GUID), представляющий ваш специализированный экземпляр Azure AD.
* `subscription-id`: Идентификатор подписки (GUID) вашей подписки Azure, в которую вы хотите, чтобы в машинах.
* `resource-group`: Имя группы ресурсов, где вы хотите, чтобы ваши подключенные машины принадлежали.
* `location`: [См. поддерживаемые регионы Azure](overview.md#supported-regions). Это местоположение может быть таким же или разным, как местоположение группы ресурсов.
* `resource-name`: (*Дополнительно*) Используется для представления ресурсов Azure вашей предприимческой машины. Если вы не укажете это значение, используется имя хоста машины.

Вы можете узнать `azcmagent` больше о инструменте командной строки, просмотрев [ссылку Azcmagent.](azcmagent-reference.md)

### <a name="windows-installation-script"></a>Скрипт установки Windows

Ниже приводится пример агента Connected Machine для сценария установки Windows, который был изменен для использования основного сервиса для поддержки полностью автоматизированной, неинтерактивной установки агента.

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

### <a name="linux-installation-script"></a>Сценарий установки Linux

Ниже приводится пример агента Connected Machine для сценария установки Linux, который был изменен для использования основного сервиса для поддержки полностью автоматизированной, неинтерактивной установки агента.

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

- Узнайте, как управлять машиной с помощью [azure Policy,](../../governance/policy/overview.md)для таких вещей, как [конфигурация гостевых VM,](../../governance/policy/concepts/guest-configuration.md)проверка того, что машина отчитывается перед ожидаемым рабочим пространством Log Analytics, позволяет осуществлять мониторинг с помощью [Azure Monitor с помощью vMs](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)и многое другое.

- Подробнее об [агенте Log Analytics.](../../azure-monitor/platform/log-analytics-agent.md) Агент Log Analytics для Windows и Linux необходим, если требуется упреждающе отслеживать ОС и рабочие нагрузки на компьютере, выполнять управление с помощью runbook службы автоматизации или с помощью решений, таких как Управление обновлениями, или использовать другие службы Azure, например, [Центр безопасности Azure](../../security-center/security-center-intro.md).
