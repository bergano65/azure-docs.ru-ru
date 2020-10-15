---
title: Управление учетной записью запуска от имени службы автоматизации Azure
description: В этой статье объясняется, как управлять учетной записью запуска от имени с помощью PowerShell или портала Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 09/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0849eb0c421883ecb0510451ff81b604538c9cc3
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92069897"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Управление учетной записью запуска от имени службы автоматизации Azure

Учетные записи запуска от имени в службе автоматизации Azure обеспечивают проверку подлинности для управления ресурсами в Azure Resource Manager или классической модели развертывания Azure с помощью модулей Runbook автоматизации и других функций автоматизации. В этой статье содержатся рекомендации по управлению учетной записью запуска от имени или классической.

Дополнительные сведения о проверке подлинности учетной записи службы автоматизации Azure и рекомендации, связанные с сценариями автоматизации процессов, [см. в](automation-security-overview.md)этой статье.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Разрешения учетной записи запуска от имени

В этом разделе определены разрешения для обычной и классической учетных записей запуска от имени.

Чтобы создать или обновить учетную запись запуска от имени, необходимо иметь привилегии и разрешения. Администратор приложений в Azure Active Directory и владелец в подписке могут выполнить все эти задачи. В следующей таблице показаны списки задач, эквивалентный командлет и необходимые разрешения для ситуаций, когда применяется разделение обязанностей.

|Задача|Командлет  |Минимальные разрешения  |Где необходимо установить разрешения|
|---|---------|---------|---|
|Создание приложения Azure AD|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Роль разработчика приложения<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>"Главная" > "Azure AD" > "Регистрация приложений" |
|Добавление учетных данных приложения|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Администратор приложений или глобальный администратор<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>"Главная" > "Azure AD" > "Регистрация приложений"|
|Создание или получение субъекта-службы Azure AD|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Администратор приложений или глобальный администратор<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>"Главная" > "Azure AD" > "Регистрация приложений"|
|Назначение или получение роли Azure для указанного субъекта|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Администратор доступа пользователей, владелец или пользователь со следующими разрешениями:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Подписка](../role-based-access-control/role-assignments-portal.md)</br>Подписки главной > > \<subscription name\> — Управление доступом (IAM)|
|Создание или удаление сертификата службы автоматизации|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Участник группы ресурсов         |Группа ресурсов учетной записи службы автоматизации|
|Создание или удаление подключения службы автоматизации|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Участник группы ресурсов |Группа ресурсов учетной записи службы автоматизации|

<sup>1</sup> Пользователи без прав администратора в арендаторе Azure AD могут [регистрировать приложения AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app), если в этом арендаторе для параметра **Пользователи могут регистрировать приложения** на странице параметров пользователя установлено значение **Да**. Если для этого параметра задано значение **Нет**, то пользователь, выполняющий данное действие, должен соответствовать требованиям в этой таблице.

Если вам назначается роль глобального администратора или соадминистратора подписки, но вы не являетесь участником экземпляра Active Directory подписки, то вы будете добавлены в качестве гостя. В этом случае `You do not have permissions to create…` на странице **Добавление учетной записи службы автоматизации** появится предупреждение.

Если вы являетесь членом Active Directory экземпляра подписки, которому назначена роль глобального администратора, вы также можете получить `You do not have permissions to create…` предупреждение на странице **Добавление учетной записи службы автоматизации** . В этом случае можно запросить удаление своего пользователя из экземпляра Active Directory подписки, а затем запросить его повторное добавление, чтобы стать полным пользователем в Active Directory.

Вот как можно убедиться, что ситуация, создающая сообщение об ошибке, исправлена.

1. На портале Azure в области Azure Active Directory выберите **Пользователи и группы**.
2. Выберите **Все пользователи**.
3. Выберите свое имя, а затем выберите **Профиль**.
4. Убедитесь, что для атрибута **Тип пользователя** в профиле пользователя не задано значение **Гость**.

### <a name="permissions-required-to-create-or-manage-classic-run-as-accounts"></a><a name="permissions-classic"></a>Разрешения, необходимые для создания классической учетной записи запуска от имени или управления ею

Чтобы настроить или обновить классические учетные записи запуска от имени, необходима роль соадминистратора на уровне подписки. Чтобы узнать больше о разрешениях классической подписки, ознакомьтесь с разделом [Классические администраторы подписок Azure](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-azure-portal"></a>Создание учетной записи запуска от имени на портале Azure

Выполните действия, с помощью которых на портале Azure можно обновить учетную запись службы автоматизации Azure. Учетные записи запуска от имени и классические учетные записи запуска от имени создаются отдельно. Если вам не нужно управлять классическими ресурсами, достаточно создать учетную запись запуска от имени Azure.

1. Войдите на портал Azure с помощью учетной записи, которая является участником роли администраторов подписки и соадминистратором подписки.

2. Найдите и выберите раздел **Учетные записи службы автоматизации**.

3. На странице учетных записей службы автоматизации выберите в списке нужную учетную запись.

4. В левой области выберите **учетные записи запуска от имени** в разделе **Параметры учетной записи** .

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="Выберите параметр Учетная запись запуска от имени.&quot;:::

5. В зависимости от требуемой учетной записи используйте панель &quot; **+ Azure: учетная запись запуска от имени** &quot; или " **Классическая учетная запись запуска от имени Azure** ". Просмотрев общие сведения, щелкните **Создать**.

    :::image type="content" source="media/manage-runas-account/automation-account-create-runas.png" alt-text="Выберите параметр Учетная запись запуска от имени.&quot;:::

5. В зависимости от требуемой учетной записи используйте панель &quot; **+ Azure: учетная запись запуска от имени** &quot; или ":::

6. Ход создания учетной записи запуска от имени в Azure можно отслеживать в разделе **Уведомления** в меню. Кроме того, отображается баннер с уведомлением о создании учетной записи. Процесс создания может занять несколько минут.

## <a name="create-a-run-as-account-using-powershell"></a>Создание учетной записи запуска от имени с помощью PowerShell

В следующем списке приведены требования для создания учетной записи запуска от имени в PowerShell с помощью предоставленного скрипта. Эти требования применяются к обоим типам учетных записей запуска от имени.

* Windows 10 или Windows Server 2016 с модулями Azure Resource Manager версии 3.4.1 и выше. Сценарий PowerShell не поддерживает более ранние версии Windows.
* Azure PowerShell PowerShell 6.2.4 или более поздней версии. Дополнительные сведения см. [в разделе Установка и настройка Azure PowerShell](/powershell/azure/install-az-ps).
* Учетная запись службы автоматизации, указанная в качестве значений параметров `AutomationAccountName` и `ApplicationDisplayName`.
* Разрешения, аналогичные списку в разделе [разрешений для настройки учетной записи запуска от имени](#permissions).

Чтобы получить значения для `AutomationAccountName` , `SubscriptionId` и `ResourceGroupName` , которые являются обязательными параметрами для скрипта PowerShell, выполните следующие действия.

1. На портале Azure выберите **Учетные записи службы автоматизации**.

1. На странице учетных записей службы автоматизации выберите нужную учетную запись.

1. В разделе параметров учетной записи выберите **Свойства**.

1. Обратите внимание на значения параметров **имя**, **идентификатор подписки**и **Группа ресурсов** на странице **Свойства** .

   ![Страница свойств учетной записи службы автоматизации](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Сценарий PowerShell для создания учетной записи запуска от имени

Сценарий PowerShell включает поддержку нескольких конфигураций.

* создание учетной записи запуска от имени с использованием самозаверяющего сертификата;
* создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата;
* Создайте учетную запись запуска от имени и классическую учетную запись запуска от имени, используя сертификат, выданный центром сертификации предприятия.
* создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата в облаке Azure для государственных организаций.

1. Скачайте и сохраните скрипт в локальной папке с помощью следующей команды.

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. Запустите PowerShell с повышенными правами пользователя и перейдите в папку, содержащую скрипт.

3. Выполните одну из приведенных ниже команд, чтобы создать учетную запись запуска от имени и (или) классический Запуск от имени в соответствии с вашими требованиями.

    * Создайте учетную запись запуска от имени с помощью самозаверяющего сертификата.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата;

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * создание учетной записи запуска от имени и классической учетной записи запуска от имени Azure с использованием корпоративного сертификата;

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        Если вы создали классическую учетную запись запуска от имени с использованием открытого корпоративного сертификата (CER-файл), используйте этот сертификат. Сценарий создает и сохраняет его в папке временных файлов на компьютере в профиле пользователя, `%USERPROFILE%\AppData\Local\Temp` который использовался для выполнения сеанса PowerShell. Ознакомьтесь с разделом [Передача сертификата управления службами Azure](../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

    * Создание учетной записи запуска от имени и классической учетной записи запуска от имени с использованием самозаверяющего сертификата в облаке Azure для государственных организаций.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. После выполнения сценария появится запрос на аутентификацию в Azure. Войдите с помощью учетной записи, которая является членом роли администраторов подписки. При создании классической учетной записи запуска от имени ваша учетная запись должна быть соадминистратором подписки.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Удаление учетной записи запуска от имени или классической учетной записи запуска от имени

В этом разделе описывается, как удалить учетную запись запуска от имени или классическую учетную запись запуска от имени. При выполнении этого действия учетная запись службы автоматизации сохраняется. После удаления учетной записи запуска от имени ее можно создать повторно в портал Azure или с помощью предоставленного скрипта PowerShell.

1. На портале Azure откройте учетную запись службы автоматизации.

2. В области слева выберите **Учетные записи запуска от имени** в разделе параметров учетной записи.

3. На странице свойств учетных записей запуска от имени выберите учетную запись запуска от имени или классическую учетную запись запуска от имени, которую нужно удалить.

4. Затем в области "Свойства" выбранной учетной записи щелкните **Удалить**.

   ![Удаление учетной записи запуска от имени](media/manage-runas-account/automation-account-delete-runas.png)

5. Ход удаления учетной записи можно отслеживать в разделе **Уведомления** в меню.

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Продление самозаверяющего сертификата

Срок действия самозаверяющего сертификата, созданного для учетной записи запуска от имени, составляет один год с даты создания. В определенный момент перед истечением срока действия учетной записи запуска от имени вам потребуется продлить сертификат. Его можно продлить в любое время до истечения его срока действия.

При продлении самозаверяющего сертификата текущий действительный сертификат сохраняется, чтобы гарантировать, что все модули runbook, которые поставлены в очередь или активно выполняются и для аутентификации которых используется учетная запись запуска от имени, не пострадают. Сертификат будет существовать до окончания срока действия.

>[!NOTE]
>Если вы считаете, что учетная запись запуска от имени была скомпрометирована, то можете удалить самозаверяющий сертификат и создать его заново.

>[!NOTE]
>Если вы настроили учетную запись запуска от имени службы автоматизации для использования сертификата, выданного корпоративным центром сертификации, и используете параметр продления самозаверяющего сертификата, то этот корпоративный сертификат будет заменен самозаверяющим сертификатом.

Чтобы продлить самозаверяющий сертификат, выполните следующие действия.

1. На портале Azure откройте учетную запись службы автоматизации.

1. Выберите **Учетные записи запуска от имени** в разделе параметров учетной записи.

    ![Область свойств учетной записи службы автоматизации](media/manage-runas-account/automation-account-properties-pane.png)

1. На странице свойств учетных записей запуска от имени выберите обычную или классическую учетную запись запуска от имени, для которой нужно продлить сертификат.

1. В области свойств выбранной учетной записи щелкните **Продлить сертификат**.

    ![Обновление сертификата для учетной записи запуска от имени](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Ход обновления сертификата можно отслеживать в разделе **Уведомления** в меню.

## <a name="limit-run-as-account-permissions"></a>Ограничение разрешений учетной записи запуска от имени

Чтобы управлять доступом службы автоматизации к ресурсам в Azure, можно запустить сценарий [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8). Этот сценарий изменяет существующий субъект-службу учетной записи запуска от имени, чтобы создать и использовать пользовательское определение роли. Это роль с разрешениями для всех ресурсов, кроме [Key Vault](../key-vault/index.yml).

>[!IMPORTANT]
>После запуска сценария **Update-AutomationRunAsAccountRoleAssignments.ps1** модули runbook, которые обращаются к Key Vault с помощью учетных записей запуска от имени, больше не будут работать. Перед выполнением сценария необходимо проверить модули runbook в вашей учетной записи на наличие вызовов к Azure Key Vault. Чтобы разрешить доступ к Key Vault из модулей runbook службы автоматизации Azure, необходимо [добавить учетную запись запуска от имени в разрешения Key Vault](#add-permissions-to-key-vault).

Если необходимо ввести дополнительные ограничения, выходящие за рамки возможностей субъекта-службы запуска от имени, можно добавить другие типы ресурсов в элемент `NotActions` пользовательского определения роли. В следующем примере ограничивается доступ к `Microsoft.Compute/*`. Если вы добавите этот тип ресурса в `NotActions` в определении роли, данная роль не сможет предоставить доступ к вычислительному ресурсу. Чтобы узнать больше об определениях ролей, ознакомьтесь с разделом [Общие сведения об определениях ролей для ресурсов Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Определить, указан ли субъект-служба, используемый вашей учетной записью запуска от имени, в определении роли участника или пользовательском определении.

1. Перейдите к учетной записи службы автоматизации и выберите **Учетные записи запуска от имени** в разделе параметров учетной записи.
2. Выберите **Azure Run As Account** (Учетная запись запуска от имени Azure).
3. Выберите **Роль**, чтобы указать используемое определение роли.

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="Выберите параметр Учетная запись запуска от имени.&quot;:::

5. В зависимости от требуемой учетной записи используйте панель &quot; **+ Azure: учетная запись запуска от имени** &quot; или " lightbox="media/manage-runas-account/verify-role-expanded.png":::

Можно также задать определение роли, используемое учетными записями запуска от имени, для нескольких подписок или учетных записей автоматизации. Для этого используйте сценарий [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) из коллекции PowerShell.

### <a name="add-permissions-to-key-vault"></a>Добавление разрешений в Key Vault

Вы можете разрешить службе автоматизации Azure проверять, используют ли Key Vault и субъект-служба учетной записи запуска от имени пользовательское определение роли. Необходимо сделать следующее:

* Предоставьте разрешения для Key Vault.
* Настройте политику доступа.

Вы можете использовать сценарий [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) из коллекция PowerShell, чтобы предоставить учетной записи запуска от имени разрешения для Key Vault. Дополнительные сведения о настройке разрешений для Key Vault см. в разделе [Назначение политики доступа Key Vault](../key-vault/general/assign-access-policy-powershell.md) .

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Устранение проблем из-за неправильной конфигурации учетных записей запуска от имени

Во время начальной настройки некоторые из элементов конфигурации, необходимых для работы обычной или классической учетной записи запуска от имени, могут быть удалены или неправильно созданы. Возможные случаи неправильной конфигурации включают в себя:

* ресурс сертификата;
* ресурс подключения;
* удаление учетной записи запуска от имени из роли участника;
* субъект-службу или приложение в Azure AD.

В случае такой неправильной конфигурации учетная запись службы автоматизации обнаружит эти изменения и отобразит в области свойств учетных записей запуска от имени состояние *Не выполнено*.

![Сообщение о том, что настройка учетной записи запуска от имени не завершена](media/manage-runas-account/automation-account-runas-config-incomplete.png)

При выборе учетной записи запуска от имени в области свойств учетной записи отобразится приведенное ниже сообщение об ошибке.

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Вы можете быстро устранить проблемы с учетной записью запуска от имени, удалив и повторно создав учетную запись запуска от имени.

## <a name="next-steps"></a>Дальнейшие действия

* [Объекты приложения и субъекта-службы](../active-directory/develop/app-objects-and-service-principals.md)
* [Общие сведения о сертификатах для Облачных служб Azure](../cloud-services/cloud-services-certs-create.md)