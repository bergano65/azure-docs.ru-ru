---
title: Включить аудит безопасности для служб доменов Azure AD (ru) Документы Майкрософт
description: Узнайте, как обеспечить центрирование аудитов безопасности для регистрации событий для анализа и оповещения в службах домена Azure AD
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: b2138818a9092999dd54b14664f7146f087c4fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328652"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Включить аудит безопасности для служб домена Active Directory

Проверки безопасности Azure Active Directory Domain Services (Azure AD DS) позволяют событиям безопасности Azure передавать события безопасности в целевые ресурсы. Эти ресурсы включают в себя рабочие области хранения Azure, рабочие области анализа журналов Azure или концентратор событий Azure. После включения событий аудита безопасности Azure AD DS отправляет все проверенные события для выбранной категории в целевой ресурс.

Вы можете архивировать события в хранилище Azure и передавать события в программное обеспечение для управления безопасностью и управление событиями (SIEM) с помощью концентраторов azure Event, или сделать свой собственный анализ и использовать рабочие области Azure Log Analytics с портала Azure.

> [!IMPORTANT]
> Проверки безопасности Azure AD DS доступны только для экземпляров, основанных на ресурсах Azure Manager. Для получения информации о [Migrate Azure AD DS from the Classic virtual network model to Resource Manager][migrate-azure-adds]том, как мигрировать, см.

## <a name="audit-event-categories"></a>Категории событий аудита

Аудит безопасности Azure AD DS согласуется с традиционным аудитом для традиционных контроллеров доменов AD DS. В гибридных средах можно повторно использовать существующие шаблоны аудита, чтобы при анализе событий можно было использовать одну и ту же логику. В зависимости от сценария, необходимого для устранения неполадок или анализа, различные категории событий аудита должны быть целевыми.

Доступны следующие категории событий аудита:

| Имя категории аудита | Описание |
|:---|:---|
| Логон учетной записи|Аудит пытается проверить подлинность данных учетной записи на контроллере домена или на локальном менеджере учетных записей безопасности (SAM).</p>Настройки и события политики Logon и Logoff отслеживают попытки доступа к определенному компьютеру. Настройки и события в этой категории фокусируются на используемой базе данных учетных записей. Эта категория включает в себя следующие подкатегории:<ul><li>[Аудит проверки учетных данных](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Аудит службы проверки подлинности Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Аудит операций с билетами службы Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Аудит других событий входа и выхода](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Управление учетными записями|Аудит изменений в учетных записях пользователей и компьютера хиноу-аккаунтах и группах. Эта категория включает в себя следующие подкатегории:<ul><li>[Аудит управления группами приложений](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Аудит управления учетными записями компьютеров](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Аудит управления группами распространения](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Аудит Другого управления счетами](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Аудит управления группами безопасности](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Аудит управления учетными записями пользователей](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Отслеживание деталей|Аудит деятельности отдельных приложений и пользователей на этом компьютере, а также понять, как используется компьютер. Эта категория включает в себя следующие подкатегории:<ul><li>[Аудит активности DPAPI](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Аудит деятельности PNP](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Аудит создания процессов](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Аудит завершения процессов](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Аудит событий RPC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Доступ к справочнику|Аудит пытается получить доступ к объектам и модифицировать объекты в службах доменов Active Directory (AD DS). Эти события аудита регистрируются только на контроллерах доменов. Эта категория включает в себя следующие подкатегории:<ul><li>[Аудит подробной репликации службы каталогов](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Аудит доступа к службе каталогов](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Аудит изменения службы каталогов](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Аудит репликации службы каталогов](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Логон-Логофф|Аудит пытается войти на компьютер в интерактивном режиме или через сеть. Эти события полезны для отслеживания активности пользователей и выявления потенциальных атак на сетевые ресурсы. Эта категория включает в себя следующие подкатегории:<ul><li>[Аудит блокировки учетных записей](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Аудит заявок пользователей или устройств на доступ](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Аудит расширенного режима IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Аудит участия в группе](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Аудит основного режима IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Аудит быстрого режима IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Аудит выхода из системы](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Аудит входа в систему](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[Аудит сервера политики сети](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Аудит других событий входа и выхода](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Аудит специального входа](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Доступ к объектам| Аудит пытается получить доступ к определенным объектам или типам объектов в сети или на компьютере. Эта категория включает в себя следующие подкатегории:<ul><li>[Аудит событий, создаваемых приложениями](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Аудит служб сертификации](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Аудит сведений об общем файловом ресурсе](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Аудит общего файлового ресурса](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Система аудита файлов](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Аудит подключения платформы фильтрации](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Аудит отбрасывания пакетов платформой фильтрации](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Манипуляция с ревизией](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Объект аудита ядра](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Аудит других событий доступа к объектам](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Реестр аудита](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Аудит съемного хранилища](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Аудит диспетчера учетных записей безопасности](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[Аудит сверки с централизованной политикой доступа](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Изменение политики|Аудит изменяет важные политики безопасности в локальной системе или сети. Политики обычно устанавливаются администраторами для обеспечения безопасности сетевых ресурсов. Мониторинг изменений или попыток изменить эти политики может быть важным аспектом управления безопасностью для сети. Эта категория включает в себя следующие подкатегории:<ul><li>[Аудит изменения политики аудита](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Аудит изменения политики проверки подлинности](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Аудит изменения политики авторизации](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Аудит изменения политики платформы фильтрации](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Аудит изменения политики на уровне правил MPSSVC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Аудит других изменений политики](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Использование привилегий| Аудит использования определенных разрешений на одной или нескольких системах. Эта категория включает в себя следующие подкатегории:<ul><li>[Аудит использования привилегий, не затрагивающих конфиденциальные данные](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Аудит использования привилегий, затрагивающих конфиденциальные данные](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Аудит других событий использования привилегий](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|Система| Аудит системных изменений на уровне компьютера, не включенных в другие категории и которые имеют потенциальные последствия для безопасности. Эта категория включает в себя следующие подкатегории:<ul><li>[Аудит драйвера IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Аудит других системных событий](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Аудит изменения состояния безопасности](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Аудит расширения системы безопасности](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Аудит целостности системы](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>Ит-икс событий для категории

 Аудит безопасности Azure AD DS записывает следующие инаки событий, когда конкретное действие запускает проверяемое событие:

| Название категории событий | Идентификаторы событий |
|:---|:---|
|Безопасность входа в журнал аккаунтов|4767, 4774, 4775, 4776, 4777|
|Безопасность управления счетами|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Безопасность отслеживания деталей|None|
|Безопасность доступа DS|5136, 5137, 5138, 5139, 5141|
|Безопасность Логон-Логофф|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Безопасность доступа к объекту|None|
|Безопасность изменения политики|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Привилегия Использование безопасности|4985|
|Безопасность системы|4612, 4621|

## <a name="security-audit-destinations"></a>Направления аудита безопасности

В качестве целевого ресурса для аудита dS-аудитов Azure AD можно использовать концентраторы azure Storage, Azure Event hubs или Azure Log Analytics. Эти направления можно комбинировать. Например, можно использовать Хранилище Azure для архивирования событий аудита безопасности, а также рабочее пространство Analytics журналов Azure для анализа и отчета об информации в краткосрочной перспективе.

В следующей таблице описаны сценарии для каждого типа ресурсов назначения.

> [!IMPORTANT]
> Перед тем, как включить аудит безопасности Azure AD DS, необходимо создать целевой ресурс. Вы можете создать эти ресурсы с помощью портала Azure, Azure PowerShell или Azure CLI.

| Целевой ресурс | Сценарий |
|:---|:---|
|Хранилище Azure| Эта цель должна использоваться, когда ваша основная потребность заключается в хранении событий аудита безопасности для архивных целей. Другие цели могут быть использованы для архивных целей, однако эти цели обеспечивают возможности, выходящие за рамки первичной потребности в архивировании. <br /><br />Перед тем, как включить события аудита безопасности Azure AD DS, сначала [создайте учетную запись хранения Azure.](../storage/common/storage-account-create.md)|
|Центры событий Azure| Эта цель должна использоваться, когда ваша основная потребность заключается в обмене событиями аудита безопасности с дополнительным программным обеспечением, таким как программное обеспечение для анализа данных или информации о безопасности & программное обеспечение для управления событиями (SIEM).<br /><br />Перед тем, как включить события аудита безопасности Azure AD DS, [создайте концентратор событий с помощью портала Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Рабочее пространство аналитики журналов Azure| Эта цель должна использоваться, когда основной потребностью является анализ и обзор безопасных аудитов с портала Azure напрямую.<br /><br />Перед тем, как включить события аудита безопасности Azure AD DS, [создайте рабочее пространство для анализа журналов на портале Azure.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Включить события аудита безопасности с помощью портала Azure

Чтобы включить события аудита безопасности Azure AD DS с помощью портала Azure, выполните следующие шаги.

> [!IMPORTANT]
> Аудит безопасности Azure AD DS не имеет обратной силы. Вы не можете получить или воспроизвести события из прошлого. DD Azure AD может отправлять события, возникающие после включения аудита безопасности.

1. Войдите на портал Azure по адресу https://portal.azure.com.
1. В верхней части портала Azure ищите **службы домена Azure AD.** Выберите управляемый домен, например *aaddscontoso.com.*
1. В окне Azure AD DS выберите **настройки диагностики** на левой стороне.
1. Диагностика не настраивается по умолчанию. Чтобы начать работу, выберите **Дополнительную диагностическую настройку.**

    ![Добавление диагностической настройки для служб доменов Azure AD](./media/security-audit-events/add-diagnostic-settings.png)

1. Введите имя для диагностической конфигурации, например *aadds-аудит.*

    Проверьте поле для назначения аудита безопасности, который вы хотите. Вы можете выбрать учетную запись хранения Azure, концентратор событий Azure или рабочее пространство для аналитики журналов. Эти ресурсы назначения уже должны существовать в подписке Azure. Вы не можете создать ресурсы назначения в этом мастере.

    ![Включить требуемое назначение и тип событий аудита для фиксации](./media/security-audit-events/diagnostic-settings-page.png)

    * **Хранилище Azure**
        * Выберите **Архив для учетной записи хранения,** а затем выберите **Настройка.**
        * Выберите учетную **запись подписки** и **хранилища,** которые вы хотите использовать для архивирования событий аудита безопасности.
        * Когда готовы, выберите **OK**.
    * **Центры событий Azure**
        * Выберите **Stream в концентратор событий,** а затем выберите **настройку.**
        * Выберите пространство имен **концентратора** **событий.** При необходимости также выберите **имя концентратора событий,** а затем **имя политика концентратора событий.**
        * Когда готовы, выберите **OK**.
    * **Аналитические рабочие области Azure Log**
        * Выберите **Send to Log Analytics,** затем выберите **рабочее пространство для** анализа **подписки** и журналов, которые вы хотите использовать для хранения событий аудита безопасности.

1. Выберите категории журналов, которые вы хотите включить для конкретного целевого ресурса. При отправке событий аудита в учетную запись Хранилища Azure можно настроить политику удержания, определяющую количество дней для хранения данных. Настройка по умолчанию *0* сохраняет все данные и не вращает события по истечении определенного периода времени.

    Можно выбрать различные категории журналов для каждого целевого ресурса в рамках одной конфигурации. Эта возможность позволяет выбрать, какие категории журналов вы хотите сохранить для журнала Analytics и какие журналы категорий, которые вы хотите архивировать, например.

1. При этом выберите **Сохранить** для фиксации изменений. Целевые ресурсы начинают получать события аудита безопасности Azure AD DS вскоре после сохранения конфигурации.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Включить события аудита безопасности с помощью Azure PowerShell

Чтобы включить события аудита безопасности Azure AD DS с помощью Azure PowerShell, выполните следующие шаги. При необходимости сначала [установите модуль Azure PowerShell и подключитесь к подписке Azure.](/powershell/azure/install-az-ps)

> [!IMPORTANT]
> Аудит безопасности Azure AD DS не имеет обратной силы. Вы не можете получить или воспроизвести события из прошлого. DD Azure AD может отправлять события, возникающие после включения аудита безопасности.

1. Попроверьте подлинность подписки Azure с помощью cmdlet [Connect-AzAccount.](/powershell/module/Az.Accounts/Connect-AzAccount) По запросу введите учетные данные учетной записи.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Создайте целевой ресурс для событий аудита безопасности.

    * **Хранилище Azure** - [Создайте учетную запись хранения с помощью Azure PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Концентраторы событий Azure** - [создают концентратор событий с помощью Azure PowerShell.](../event-hubs/event-hubs-quickstart-powershell.md) Возможно, вам также потребуется использовать смдlet [New-AzEventHubAuthorization](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) для создания правила авторизации, которое предоставляет разрешения Azure AD DS *в пространстве имен*концентратора событий. Правило авторизации должно включать права **управления,** **прослушивания**и **отправки.**

        > [!IMPORTANT]
        > Убедитесь, что вы установите правило авторизации в пространстве имен концентратора событий, а не в самом концентраторе событий.

    * **Аналитические рабочие области** - Azure Log[создают рабочее пространство для анализа журналов с помощью Azure PowerShell.](../azure-monitor/learn/quick-create-workspace-posh.md)

1. Получите идентификатор ресурсов для управляемого домена Azure AD DS с помощью cmdlet [Get-AzResource.](/powershell/module/Az.Resources/Get-AzResource) Создайте переменную, названную *$aadds. ResourceId* для удержания значения:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Настройте настройки Azure Diagnostic с помощью cmdlet [Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) для использования целевого ресурса для событий аудита безопасности azure AD Domain Services. В следующих примерах переменная *$aadds. ResourceId* используется с предыдущего шага.

    * **Хранилище Azure** - Заменить *хранилищеAccountId* на имя учетной записи хранилища:

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Концентраторы событий Azure** - Заменить *eventHubName* с именем вашего концентратора событий и *eventHubRuleId* идентификатором правил авторизации:

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Аналитические рабочие области Azure Log** - Заменить *workspaceId* идентификатором рабочего пространства log Analytics:

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Запрос и просмотр событий аудита безопасности с помощью Azure Monitor

Рабочие области входа в журналы позволяют просматривать и анализировать события аудита безопасности с помощью Azure Monitor и языка запросов Kusto. Этот язык запросов предназначен для использования только для чтения, который может похвастаться аналитическими возможностями с простым для чтения синтаксисом. Для получения дополнительной информации, чтобы начать работу с языками запроса Kusto, см.

* [Документация по Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
* [Начало работы со службой Log Analytics в Azure Monitor](../azure-monitor/log-query/get-started-portal.md)
* [Начало работы с запросами к журналам Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
* Узнайте, как [создавать панели мониторинга данных Log Analytics и предоставлять к ним общий доступ](../azure-monitor/learn/tutorial-logs-dashboards.md).

Следующие выборочные запросы могут быть использованы для начала анализа событий аудита безопасности с Azure AD DS.

### <a name="sample-query-1"></a>Пример запроса 1

Просмотр всех событий блокировки учетной записи за последние семь дней:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Пример запроса 2

Посмотреть все события блокировки учетной записи (*4740*) между 3 февраля 2020 в 9 утра и 10 февраля 2020 полночь, отсортированы восходящей по дате и времени:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-02-03 09:00) and TimeGenerated <= datetime(2020-02-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Пример запроса 3

Просмотр событий регистрации учетной записи семь дней назад (с этого момента) для пользователя, названного пользователем:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Пример запроса 4

Просмотр событий регистрации учетной записи семь дней назад с этого момента для учетной записи, названной пользователем, который пытался войти в систему с помощью плохого пароля (*0xC0000006a):*

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Пример запроса 5

Просмотр событий регистрации учетной записи семь дней назад с этого момента для учетной записи, названной пользователем, который пытался войти в систему, в то время как учетная запись была заблокирована (*0xC0000234):*

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Пример запроса 6

Просмотр числа событий регистрации учетной записи семь дней назад для всех попыток входной связи, которые произошли для всех заблокированных пользователей:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="next-steps"></a>Дальнейшие действия

Для получения конкретной информации о Kusto, см.

* [Обзор](/azure/kusto/query/) языка запросов Kusto.
* [Kusto учебник,](/azure/kusto/query/tutorial) чтобы ознакомить вас с основами запроса.
* [Примеры запросов,](/azure/kusto/query/samples) которые помогут вам узнать новые способы просмотра данных.
* Kusto [передовой практики](/azure/kusto/query/best-practices) для оптимизации ваших запросов на успех.

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md
