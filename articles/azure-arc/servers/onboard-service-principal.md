---
title: Подключение гибридных компьютеров к Azure в масштабе
description: Из этой статьи вы узнаете, как подключить компьютеры к Azure с помощью дуги Azure для серверов (Предварительная версия) с помощью субъекта-службы.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 3a19dc019d2566ddddb2c0ba7988b342d30a45d4
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192276"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Подключение гибридных компьютеров к Azure в масштабе

Вы можете включить дугу Azure для серверов (Предварительная версия) для нескольких компьютеров Windows или Linux в вашей среде с несколькими гибкими возможностями в зависимости от требований. Используя сценарий шаблона, который мы предоставляем, можно автоматизировать каждый шаг установки, включая установку подключения к службе "Дуга Azure". Тем не менее, вам потребуется интерактивно выполнить этот скрипт с учетной записью, обладающей повышенными разрешениями на целевом компьютере и в Azure. Чтобы подключить компьютеры к службе "Дуга Azure" для серверов, можно использовать [субъект-службу](../../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory вместо использования привилегированного удостоверения для [интерактивного подключения компьютера](onboard-portal.md). Субъект-служба — это специальное ограниченное удостоверение управления, которое предоставляет только минимальное разрешение, необходимое для подключения компьютеров к Azure с помощью команды `azcmagent`. Это более безопасно, чем использование учетной записи с более высоким уровнем привилегий, такой как администратор клиента, и соответствует рекомендациям по обеспечению безопасности управления доступом. Субъект-служба используется только во время адаптации, но не используется для других целей.  

Методы установки для установки и настройки агента подключенного компьютера требуют, чтобы используемый автоматизированный метод получил разрешения администратора на компьютерах. В Linux с использованием учетной записи root и Windows в качестве члена локальной группы администраторов.

Прежде чем начать, обязательно ознакомьтесь с [предварительными](overview.md#prerequisites) требованиями и убедитесь, что подписка и ресурсы соответствуют требованиям.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

По завершении этого процесса вы сможете успешно подключить гибридные компьютеры к службе "Дуга Azure" для серверов.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Создание субъекта-службы для адаптации в масштабе

С помощью [Azure PowerShell](/powershell/azure/install-az-ps) можно создать субъект-службу с помощью командлета [New азадсервицепринЦипал](/powershell/module/Az.Resources/New-AzADServicePrincipal) . Также можно выполнить действия, описанные в разделе [Создание субъекта-службы с помощью портал Azure](../../active-directory/develop/howto-create-service-principal-portal.md) для выполнения этой задачи.

> [!NOTE]
> При создании субъекта-службы учетная запись должна быть владельцем или администратором доступа пользователей в подписке, которую вы хотите использовать для адаптации. Если у вас нет достаточных разрешений для создания назначений ролей, вы сможете создать субъект-службу, но вам не удастся подключить компьютеры.
>

Чтобы создать субъект-службу с помощью PowerShell, выполните следующие действия.

1. Выполните следующую команду. Необходимо сохранить выходные данные командлета [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) в переменной, иначе вы не сможете получить пароль, который потребуется на более позднем этапе.

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

2. Чтобы получить пароль, хранящийся в переменной `$sp`, выполните следующую команду:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. В выходных данных найдите значение пароля под полем **пароль** и скопируйте его. Кроме того, найдите значение в поле **applicationId** и скопируйте его. Сохраните их для последующего использования в безопасном месте. Если вы забыли или потеряли пароль субъекта-службы, его можно сбросить с помощью командлета [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) .

Значения из следующих свойств используются с параметрами, передаваемыми в `azcmagent`:

* Значение свойства **applicationId** используется для значения параметра `--service-principal-id`
* Значение из свойства **Password** используется для параметра `--service-principal-secret`, используемого для подключения агента.

> [!NOTE]
> Убедитесь, что используется свойство **applicationId** субъекта-службы, а не свойство **ID** .
>

Роль **адаптации компьютера, подключенного к Azure** , содержит только разрешения, необходимые для подключения компьютера. Вы можете назначить разрешение субъекта-службы, чтобы в его области можно было включить группу ресурсов или подписку. Чтобы добавить назначение ролей, см. раздел [Добавление и удаление назначений ролей с помощью Azure RBAC и портал Azure](../../role-based-access-control/role-assignments-portal.md) или [Добавление или удаление назначений ролей с помощью azure RBAC и Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="install-the-agent-and-connect-to-azure"></a>Установка агента и подключение к Azure

Следующие шаги устанавливают и настраивают агент подключенного компьютера на гибридных компьютерах с помощью шаблона скрипта, который выполняет аналогичные действия, описанные в статье [подключение гибридных компьютеров к Azure из портал Azure](onboard-portal.md) статьи. Разница заключается в последнем шаге установки подключения к службе "Дуга Azure" с помощью команды `azcmagent`, использующей субъект-службу. 

Ниже приведены параметры, которые можно настроить для использования в качестве субъекта-службы `azcmagent`.

* `tenant-id`: уникальный идентификатор (GUID), представляющий выделенный экземпляр Azure AD.
* `subscription-id`: идентификатор подписки (GUID) подписки Azure, в которой должны быть компьютеры.
* `resource-group`: имя группы ресурсов, к которой должны принадлежать подключенные компьютеры.
* `location`: см. раздел [Поддерживаемые регионы Azure](overview.md#supported-regions). Это расположение может совпадать с расположением группы ресурсов или совпадать с ним.
* `resource-name` (*необязательно*) используется для представления ресурсов Azure локального компьютера. Если это значение не указано, используется имя узла компьютера.

Дополнительные сведения о средстве командной строки `azcmagent` см. в [справочнике по азкмажент](azcmagent-reference.md).

### <a name="windows-installation-script"></a>Сценарий установки Windows

Ниже приведен пример скрипта установки агента подключенных компьютеров для Windows, который был изменен для использования субъекта-службы для поддержки полностью автоматизированной, неинтерактивной установки агента.

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

Ниже приведен пример сценария установки агента подключенных компьютеров для Linux, который был изменен для использования субъекта-службы для поддержки полностью автоматизированной, неинтерактивной установки агента.

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

После установки агента и его настройки для подключения к службе "Дуга Azure для серверов (Предварительная версия)" перейдите к портал Azure, чтобы убедиться, что сервер успешно подключен. Просмотр компьютеров в [портал Azure](https://aka.ms/hybridmachineportal).

![Успешное соединение с сервером](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Следующие шаги

- Узнайте, как управлять компьютером с помощью [политики Azure](../../governance/policy/overview.md), например [гостевой конфигурации](../../governance/policy/concepts/guest-configuration.md)виртуальной машины, проверки того, что компьютер сообщает о предполагаемой log Analytics рабочей области, включить мониторинг с помощью [Azure Monitor с виртуальными машинами](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)и многое другое.

- Дополнительные сведения об [агенте log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Агент Log Analytics для Windows и Linux необходим, если требуется заблаговременно отслеживать ОПЕРАЦИОНную систему и рабочие нагрузки, выполняемые на компьютере, управлять ею с помощью модулей Runbook автоматизации или таких решений, как Управление обновлениями, или использовать другие службы Azure, такие как [Центр безопасности Azure](../../security-center/security-center-intro.md).
