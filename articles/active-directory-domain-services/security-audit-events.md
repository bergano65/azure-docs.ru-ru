---
title: Включение аудита безопасности для доменных служб Azure AD | Документация Майкрософт
description: Включение аудита безопасности для доменных служб Azure AD
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: 3105296b3c670d3d44789c93878fa1fc6076973b
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566499"
---
# <a name="enable-security-audits-for-azure-ad-domain-services-preview"></a>Включение аудита безопасности для доменных служб Azure AD (Предварительная версия)
Аудит безопасности службы Azure домена AD позволяет пользователям с помощью портала службы доменов Azure AD для потоковой передачи событий аудита безопасности к целевым ресурсам. Ресурсы, которые могут получать эти события включают службы хранилища Azure, рабочие области Azure Log Analytics или концентратор событий Azure. Вскоре после включения аудита событий безопасности, службы доменов Azure AD отправляет все события аудита для выбранной категории к целевому источнику. События аудита безопасности позволяют клиентам архивировать аудиту события в хранилище Azure. Кроме того клиенты могут поток событий в безопасности информации и событий программное обеспечение для управления (SIEM) (или эквивалент) с помощью концентраторов событий или создайте собственные аналитики и аналитические данные, используя Azure Log Analytics на портале Azure. 

> [!IMPORTANT]
> Azure аудит безопасности доменных служб AD доступен только в экземплярах на основе Azure Resource Manager для доменных служб Azure AD.
>
>

## <a name="auditing-event-categories"></a>Категории событий аудита
Azure аудит безопасности доменных служб AD выравнивает с традиционной аудитом, поставляемом контроллеров домена доменных служб Active Directory. Повторное использование существующих шаблонов аудита гарантирует, что при анализе событий можно использовать ту же логику. Аудит безопасности Azure AD Domain Services имеются следующие категории событий.

| Имя категории аудита | Описание |
|:---|:---|
| Вход в учетную запись|Аудит попыток проверки подлинности данные учетной записи на контроллере домена или на локальном диспетчера учетных записей (SAM).</p>Входа в систему и выхода из системы параметры политики и события отслеживать попытки доступа к определенного компьютера. Параметры и события в этой категории сфокусирована на базы данных учетных записей, который используется. В эту категорию входят следующие подкатегории:<ul><li>[Аудит проверки учетных данных](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Аудит службы проверки подлинности Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Аудит операций с билетами службы Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Аудит других событий входа/выхода](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Управление учетными записями|Аудит изменения для учетных записей пользователей и компьютеров и групп. В эту категорию входят следующие подкатегории:<ul><li>[Аудит управления группами приложений](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Аудит управления учетными записями компьютера](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Аудит управления группами распространения](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Аудит других управления учетными записями](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Аудит управления группами безопасности](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Аудит управления учетными записями пользователя](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Отслеживание сведений о|Аудит действий пользователей, на этом компьютере и сведения об использовании компьютера и отдельных приложений. В эту категорию входят следующие подкатегории:<ul><li>[Аудит активности DPAPI](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Аудит активности PNP](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Аудит создания процессов](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Аудит завершения процессов](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Аудит событий RPC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Доступ служб каталогов|Аудит попыток для доступа и изменения объектов в доменных службах Active Directory (AD DS). Данные аудита, события регистрируются только на контроллерах домена. В эту категорию входят следующие подкатегории:<ul><li>[Аудит подробной репликации службы каталогов](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Доступ к службе каталогов аудита](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Аудит изменения службы каталогов](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Аудит репликации службы каталогов](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Вход выход|Аудит попыток входа на компьютер в интерактивном режиме или по сети. Эти события полезны для отслеживания активности пользователей и выявления потенциальных атак на сетевые ресурсы. В эту категорию входят следующие подкатегории:<ul><li>[Аудит блокировки учетных записей](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Аудит утверждений пользователя или устройства](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Аудит расширенного режима IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Членство в группе аудита](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Аудит основного режима IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Аудит быстрого режима IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Аудит выхода из системы](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Аудит входа в систему](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[Аудит сервера политики сети](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Аудит других событий входа/выхода](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Аудит специального входа](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Доступ к объектам| Аудит попыток доступа к определенных объектов или типов объектов в сети или компьютера. В эту категорию входят следующие подкатегории:<ul><li>[Создано приложением аудита](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Аудит служб сертификации](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Аудит подробные файлового ресурса](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Аудит общего файлового ресурса](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Аудит файловой системы](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Аудит подключения платформы фильтрации](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Аудит отбрасывания пакетов платформой фильтрации](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Дескрипторами аудита](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Аудит объектов ядра](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Аудит других событий доступа к объекту](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Аудит реестра](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Аудит съемных носителей](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Аудит диспетчера учетных записей безопасности](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[Сверка с политикой аудита централизованного доступа](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Изменение политики|Аудит изменения политики безопасности в локальной системе или в сети. Политики обычно устанавливаются с администраторам справочные ресурсы защищенной сети. Отслеживание изменений, или попытки изменения этих политик может быть важным аспектом управления безопасностью для сети. В эту категорию входят следующие подкатегории:<ul><li>[Аудит изменения политики аудита](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Аудит изменения политики проверки подлинности](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Аудит изменения политики авторизации](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Аудит изменения политики платформы фильтрации](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Аудит изменения политики на уровне правил MPSSVC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Другое изменение политики аудита](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Использование прав| Проверяет использование номеров определенные разрешения для одной или нескольких систем. В эту категорию входят следующие подкатегории:<ul><li>[Аудит использования привилегий неконфиденциальные](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Аудит использования привилегий, затрагивающих конфиденциальные](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Аудит других событий использования привилегий](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|системный;| Изменения системного уровня аудита на компьютере, не включенные в другие категории, которые имеют возможные проблемы безопасности. В эту категорию входят следующие подкатегории:<ul><li>[Аудит драйвера IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Аудит других системных событий](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Аудит изменения состояния безопасности](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Аудит расширения системы безопасности](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Аудит целостности системы](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>Идентификаторы событий по категориям
 Аудит безопасности Azure доменные службы AD записывает следующие коды событий, когда определенное действие запускает событие, подлежащее аудиту.

| Имя категории событий | Идентификаторы событий |
|:---|:---|
|Безопасность учетной записи входа в систему|4767, 4774, 4775, 4776, 4777|
|Безопасность учетной записи управления|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Подробные сведения о безопасности для отслеживания|Нет|
|Управление доступом для доменных служб Active Directory|5136, 5137, 5138, 5139, 5141|
|Вход-выход из системы безопасности|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Управление доступом для объекта|Нет|
|Политика изменения безопасности|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Использование средств обеспечения безопасности прав доступа|4985|
|Безопасность системы|4612, 4621|

## <a name="enable-security-audit-events"></a>Включение аудита событий безопасности
Следующее руководство поможет вам успешно подписаться на события аудита безопасности доменных служб Azure AD.

> [!IMPORTANT]
> Записи об аудите безопасности доменных служб AD Azure не имеет обратной силы. Не удается получить события из ранее или чтобы воспроизвести события в прошлом. Служба может отправлять только события, происходящие после включения.
>

### <a name="choose-the-target-resource"></a>Выберите целевой ресурс
Любое сочетание хранилища Azure, концентраторов событий или рабочие области Azure Log Analytics можно использовать как целевой ресурс для вашей аудита безопасности. Рассмотрим следующую таблицу для самый лучший ресурс, для варианта использования.

> [!IMPORTANT]
> Необходимо создать целевой ресурс, прежде чем включить аудит безопасности доменных служб Azure AD.
>

| Целевой ресурс | Сценарий |
|:---|:---|
|Хранилище Azure|Рассмотрите возможность использования данного целевого объекта при первичной необходимость хранить события аудита безопасности для архивирования. Другие целевые объекты, которые могут использоваться для архивации; Тем не менее эти целевые объекты предоставляют возможностей в дополнение к основной необходимость архивации. Чтобы создать учетную запись хранилища Azure, выполните [создать учетную запись хранилища.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal#create-a-storage-account-1)|
|Центры событий Azure|Рассмотрите возможность использования данного целевого объекта при вашей основной потребностью для совместного использования события аудита безопасности с помощью дополнительного программного обеспечения, таких как программное обеспечение для анализа данных или программных систем безопасности, сведения о & событий управления (SIEM). Чтобы создать концентратор событий, выполните [краткое руководство: Создание концентратора событий, с помощью портала Azure.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Рабочая область Azure Log Analytics|Рассмотрите возможность использования данного целевого объекта при вашей основной потребностью для анализа и просмотрите безопасного аудита на портале Azure напрямую.  Чтобы создать рабочую область Log Analytics, выполните [создать рабочую область Log Analytics на портале Azure.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="using-the-azure-portal-to-enable-security-audit-events"></a>Включение аудита событий безопасности с помощью портала Azure 
1. Войдите на портал Azure по адресу https://portal.azure.com.  На портале Azure выберите все службы. В списке ресурсов введите **домена**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Нажмите кнопку **доменные службы Azure AD**.
2. Щелкните экземпляр доменных служб Azure AD из списка.
3. Нажмите кнопку **параметры диагностики (Предварительная версия)** из списка действий слева.</p>
![Действие параметра диагностики](./media/security-audit-events/diagnostic-settings-action.png)
4. Введите имя конфигурации диагностики (**аудита aadds** в качестве примера).</p>
![Страница параметров диагностики](./media/security-audit-events/diagnostic-settings-page.png)
5. Установите соответствующий флажок рядом с целевым ресурсам, которые будут использоваться с события аудита безопасности.
    > [!NOTE]
    > Не удается создать целевые ресурсы на этой странице.
    >
    
    **хранилище Azure:**</p>
    Выберите **архивировать в учетной записи хранения**. Нажмите **Настроить**. Выберите **подписки** и **учетной записи хранения** вы хотите использовать для архивации аудита событий безопасности. Последовательно выберите **ОК**.</p>
    
    ![Параметры хранения для диагностики](./media/security-audit-events/diag-settings-storage.png)
    
    **Концентраторы событий Azure:**</p>
    Выберите **Stream в концентратор событий**. Нажмите **Настроить**. В **страница концентратора событий выберите**выберите **подписки** используется для создания концентратора событий. Затем выберите **пространство имен концентратора событий**, **имя концентратора событий**, и **имя политики концентратора событий**. Последовательно выберите **ОК**.</p>
    ![Параметры концентратора событий диагностики](./media/security-audit-events/diag-settings-eventhub.png)
    
    **Azure Log аналитические рабочие области:**</p>
    Установите флажок **Отправить в Log Analytics**. Выберите **подписки** и **рабочей области Log Analytics** используется для хранения событий аудита безопасности.</p>
    ![параметры диагностики рабочей области](./media/security-audit-events/diag-settings-log-analytics.png)

6. Выберите категории журналов, которые требуется включить для определенного целевого ресурса. При использовании учетных записей хранения, можно настроить политики хранения.

    > [!NOTE]
    > Вы можете выбрать категории различных журналов для каждого целевого ресурса в одну конфигурацию. Это позволяет выбрать, какие журналы категорий, которые вы хотите сохранить для Log Analytics и который категории журналов необходимо архивировать.
    >

7. Нажмите кнопку **Сохранить** для фиксации изменений. Целевые ресурсы будут получать события аудита безопасности доменных служб Azure AD вскоре после сохранить конфигурацию.

## <a name="using-azure-powershell-to-enable-security-audit-events"></a>С помощью Azure PowerShell для включения аудита событий безопасности
 
### <a name="prerequisites"></a>Технические условия

Выполните инструкции из статьи [Установка и настройка Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="enable-security-audits"></a>Включение аудита безопасности

1. Проверка подлинности для Azure Resource Manager для соответствующего клиента и подписки с помощью **Connect AzAccount** командлет Azure PowerShell.
2. Создайте целевой ресурс для безопасности событий аудита.</p>
    **хранилище Azure:**</p>
    Выполните [создать учетную запись хранилища](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-powershell) для создания учетной записи хранения.</p>
    **Концентраторы событий Azure:**</p>
    Выполните [краткое руководство: Создание концентратора событий с помощью Azure PowerShell](https://docs.microsoft.com/azure/event-hubs/event-hubs-quickstart-powershell) для создания концентратора событий. Также может потребоваться использовать [New AzEventHubAuthorizationRule](https://docs.microsoft.com/powershell/module/az.eventhub/new-azeventhubauthorizationrule?view=azps-2.3.2) командлет Azure PowerShell, чтобы создать правило авторизации для предоставления разрешений Active Directory доменные службы в концентратор событий **пространства имен**. Необходимо включить в правило авторизации **управление**, **прослушивания**, и **отправки** права.
    > [!IMPORTANT]
    > Убедитесь, что вы настраиваете правило авторизации на пространство имен концентратора событий и не концентратор событий.
       
    </p>
    
    **Azure Log аналитические рабочие области:**</p>
    Выполните [создать рабочую область Log Analytics с помощью Azure PowerShell](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace-posh) для создания рабочей области.
3. Получите идентификатор ресурса для своего экземпляра доменных служб Azure AD. В консоли Windows PowerShell открыт, прошедшего проверку подлинности введите следующую команду. Используйте **$aadds. ResourceId** переменной в качестве параметра идентификатор ресурса служб доменов Azure AD для будущих командлетов.
    ```powershell
    $aadds = Get-AzResource -name aaddsDomainName
    ``` 
4. Используйте **AzDiagnosticSetting набора** командлет, чтобы настроить параметры диагностики Azure для использования целевой ресурс для события аудита безопасности доменных служб Azure AD. В примерах ниже $aadds переменной. Идентификатор ресурса представляет идентификатор ресурса экземпляра доменных служб Azure AD (см. шаг 3).</p>
    **хранилище Azure:**
    ```powershell
    Set-AzDiagnosticSetting `
    -ResourceId $aadds.ResourceId` 
    -StorageAccountId storageAccountId `
    -Enabled $true
    ```
    Замените *storageAccountId* с хранилищем идентификатором учетной записи.</p>
    
    **Концентраторы событий Azure:**
    ```powershell
    Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -EventHubName eventHubName `
    -EventHubAuthorizationRuleId eventHubRuleId `
    -Enabled $true
    ```
    Замените *eventHubName* с именем концентратора событий. Замените *eventHubRuleId* своим идентификатором правила авторизации, созданный ранее.</p>
    
    **Azure Log аналитические рабочие области:**
    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -WorkspaceID workspaceId `
    -Enabled $true
    ```
    Замените *workspaceId* с Идентификатором рабочей области Log Analytics, созданную ранее. 

## <a name="view-security-audit-events-using-azure-monitor"></a>Просмотр событий аудита безопасности, с помощью Azure Monitor
Аналитические рабочие области log позволяют просматривать и анализировать события аудита безопасности, с помощью Azure Monitor и язык запросов Kusto. Язык запросов предназначен для использования только для чтения, который может похвастаться Аналитические возможности электропитания с синтаксисом удобный для чтения.
Ниже приведены некоторые ресурсы, которые помогут вам приступить к работе с языками запросов Kusto.
* [Документация по Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
* [Начало работы с Log Analytics в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)
* [Начало работы с запросов к журналу в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)
* [Создание и совместное использование панелей мониторинга данных Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards)

## <a name="sample-queries"></a>Примеры запросов

### <a name="sample-query-1"></a>Образец запроса 1
Все учетной записи блокировки события за последние семь дней.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Образец запроса 2
Все событиях блокировки учетной записи (4740) между 26 июня 2019 года в 09: 00 и 1 июля 2019 г. (полночь), отсортированные по возрастанию по дате и времени.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01) 
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Образец запроса 3
Учетная запись журнала о событиях семи дней назад (через) для учетной записи с именем пользователя.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Пример запроса 4
События входа семи дней назад из теперь для учетной записи с именем пользователя, который использовался для входа с помощью недопустимого пароля (0xC0000006a).
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Образец запроса 5
События входа семи дней назад из теперь для учетной записи с именем пользователя, который использовался для входа во время блокировки учетной записи (0xC0000234).
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Образец запроса 6
Количество событий входа в систему семи дней назад из теперь для всех попытках входа произошла для всех заблокированных пользователей.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="related-content"></a>Связанная информация
* [Общие сведения о](https://docs.microsoft.com/azure/kusto/query/) язык запросов Kusto.
* [Учебник Kusto](https://docs.microsoft.com/azure/kusto/query/tutorial) познакомить вас с основами запроса.
* [Примеры запросов](https://docs.microsoft.com/azure/kusto/query/samples) , помогающие вам изучать новые способы просмотра данных.
* Kusto [рекомендации](https://docs.microsoft.com/azure/kusto/query/best-practices) — оптимизировать запросы для достижения успеха.














 
