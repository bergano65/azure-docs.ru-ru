---
title: Create a resilient access control management strategy - Azure AD
description: Этот документ содержит руководство о выборе организацией стратегий для уменьшения риска блокировки во время непредвиденных сбоев
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 12/19/2018
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 478cccb3a8235291a4c4f0566cd130b4b75dbe6b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74208555"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Создание устойчивой стратегии управления доступом с помощью Azure Active Directory

>[!NOTE]
> Информация, содержащаяся в этом документе, являет собой текущее представление корпорации Майкрософт о вопросах, которые обсуждались на момент публикации. Поскольку корпорация Майкрософт должна реагировать на изменение рыночных условий, эта информация не должна рассматриваться как обязательство корпорации Майкрософт. Корпорация Майкрософт не может гарантировать достоверность информации, предоставленной после момента публикации.

Организации, использующие единое управление доступом, такое как многофакторная проверка подлинности (MFA) или единое расположение в сети, для защиты своих ИТ-систем, подвержены сбоям доступа к приложениям и ресурсам, если это единое управление доступом отключено или неправильно настроено. Например, стихийное бедствие может привести к отключению больших сегментов телекоммуникационной инфраструктуры или корпоративных сетей. Такое прерывание работы может помешать пользователям и администраторам войти.

Этот документ содержит руководство о выборе организацией стратегий для уменьшения риска блокировки во время непредвиденных сбоев со следующими сценариями:

 1. Организации могут повысить свою устойчивость, чтобы снизить риск блокировки **до сбоя**, внедряя стратегии устранения рисков или планы на непредвиденные случаи.
 2. Организации могут продолжать получать доступ к приложениям и ресурсам, которые они выбирают **во время сбоя**, благодаря наличию стратегий устранения рисков и планов на непредвиденный случай.
 3. Организации должны удостовериться, что они сохраняют информацию (например, журналы) **после сбоя** и до того, как они откатят реализованные ограничения.
 4. Организации, которые не внедрили стратегии защиты или альтернативные планы, могут реализовать **параметры аварийного режима**, чтобы справиться с перебоями.

## <a name="key-guidance"></a>Основное руководство

В этом документе четыре основных вывода:

* Избегайте блокировки администратора с помощью учетных записей доступа в чрезвычайных ситуациях.
* Implement MFA using Conditional Access (CA) rather than per-user MFA.
* Mitigate user lockout by using multiple Conditional Access (CA) controls.
* Уменьшите риск блокировки пользователя, подготовив множество методов проверки подлинности или эквиваленты для каждого пользователя.

## <a name="before-a-disruption"></a>Перед сбоем

Уменьшение рисков фактического сбоя должно быть основной задачей организации в решении возможных проблем в управлении доступом. Уменьшение рисков включает в себя планирование фактического события плюс реализацию стратегий, чтобы гарантировать, что во время сбоев управление доступом и операции не пострадают.

### <a name="why-do-you-need-resilient-access-control"></a>Зачем вам нужно устойчивое управление доступом?

 Удостоверение — это плоскость управления пользователей, обращающихся к приложениям и ресурсам. Ваше системное удостоверение контролирует, какие пользователи получают доступ к приложениям и при каких условиях (например, элементы управления доступом или требования к проверке подлинности) они это делают. Если пользователи не могут выполнить проверку подлинности в соответствии с одним или несколькими требованиями к аутентификации или управлению доступом из-за непредвиденных обстоятельств, организации могут столкнуться с одной или обеими из этих проблем:

* **Administrator lockout:** Administrators can’t manage the tenant or services.
* **User lockout:** Users can’t access apps or resources.

### <a name="administrator-lockout-contingency"></a>Непредвиденный случай блокировки администратора

Чтобы разблокировать доступ администратора к клиенту, вам необходимо создать учетные записи для аварийного доступа. Эти учетные записи для аварийного доступа, также известные как учетные записи *разбитого стекла*, предоставляют доступ к управлению конфигурацией Azure AD, когда обычные процедуры доступа к привилегированным учетным записям недоступны. Необходимо создать по крайней мере две учетные записи для аварийного доступа в соответствии с [рекомендациями, что касаются учетной записи для аварийного доступа ]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

### <a name="mitigating-user-lockout"></a>Уменьшение риска блокировки пользователя

 To mitigate the risk of user lockout, use Conditional Access policies with multiple controls to give users a choice of how they will access apps and resources. Предоставляя пользователю выбор между, например, входом с MFA **или** входом с управляемого устройства **или** входом из корпоративной сети, если один из элементов управления доступом недоступен, у пользователя есть другие варианты, чтобы продолжить работу.

#### <a name="microsoft-recommendations"></a>Рекомендации корпорации Майкрософт

Incorporate the following access controls in your existing Conditional Access policies for organization:

1. Подготовьте для каждого пользователя несколько методов проверки подлинности, которые используют разные коммуникационные каналы, например приложение Microsoft Authenticator (онлайн), OATH-токен (генерированный на устройстве) и SMS (телефонный).
2. Разверните Windows Hello для бизнеса на устройствах Windows 10, чтобы соответствовать требованиям MFA непосредственно после входа в систему.
3. Используйте доверенные устройства через [гибридное присоединение устройств к Azure AD](https://docs.microsoft.com/azure/active-directory/devices/overview) или [управляемые устройства Microsoft Intune](https://docs.microsoft.com/intune/planning-guide). Доверенные устройства улучшат взаимодействие с пользователем, поскольку само доверенное устройство может соответствовать строгим требованиям проверки подлинности к политике без вызова MFA для пользователя. Тогда MFA потребуется при регистрации нового устройства и при доступе к приложениям или ресурсам с ненадежных устройств.
4. Вместо фиксированных политик MFA используйте политики защиты идентификации Azure AD на основе рисков, которые предотвращают доступ, когда пользователь или вход в систему подвержен риску.

>[!NOTE]
> Политики на основе рисков требуют лицензий [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/).

В следующем примере описываются политики, которые вам необходимо создать, чтобы предоставить пользователю устойчивое управление доступом к своим приложениям и ресурсам. В этом примере вам потребуется группа безопасности **AppUsers** с целевыми пользователями, которым вы хотите предоставить доступ, группа с именем **CoreAdmins** с основными администраторами и группа с именем **EmergencyAccess** с учетными записями для аварийного доступа.
Этот пример набора политик предоставит выбранным пользователям в **AppUsers** доступ к выбранным приложениям, если они подключаются с доверенного устройства, ИЛИ обеспечит строгую проверку подлинности, например MFA. Это исключает учетные записи для аварийного доступа и основных администраторов.

**Набор политик устранения рисков условного доступа:**

* Policy 1: Block access to people outside target groups
  * Users and Groups: Include all users. исключает AppUsers, CoreAdmins и EmergencyAccess
  * Cloud Apps: Include all apps
  * Conditions: (None)
  * Grant Control: Block
* Policy 2: Grant access to AppUsers requiring MFA OR trusted device.
  * Users and Groups: Include AppUsers. исключает CoreAdmins и EmergencyAccess
  * Cloud Apps: Include all apps
  * Conditions: (None)
  * Grant Control: Grant access, require multi-factor authentication, require device to be compliant. For multiple controls: Require one of the selected controls.

### <a name="contingencies-for-user-lockout"></a>Непредвиденные случаи для блокировки пользователя

Кроме того, ваша организация может также создать политики на случай непредвиденных обстоятельств. Для создания политик на случай непредвиденных обстоятельств необходимо определить критерии компромисса между непрерывностью бизнеса, эксплуатационными затратами, финансовыми затратами и рисками безопасности. Например, вы можете активировать политику на случай непредвиденных обстоятельств только для подмножества пользователей, приложений, клиентов или из подмножества расположений. Политики на случай непредвиденных обстоятельств предоставят администраторам и пользователям доступ к приложениям и ресурсам во время сбоя, когда не был применен метод устранения рисков.
Распознавание вашего воздействия во время сбоя помогает снизить риск и является критически важной частью вашего процесса планирования. Чтобы создать план на непредвиденные случаи, сначала определите следующие бизнес-требования вашей организации:

1. Determine your mission critical apps ahead of time: What are the apps that you must give access to, even with a lower risk/security posture? Составьте список этих приложений и убедитесь, что все другие заинтересованные лица (бизнес, безопасность, юристы, руководство) согласны с тем, что если все управление доступом исчезнет, эти приложения все равно должны продолжать работу. В конечном итоге вы, скорее всего, получите такие категории:
   * **Категория 1 — критически важные приложения**, которые не могут быть недоступны больше чем несколько минут, например приложения, которые напрямую влияют на доходы организации.
   * **Категория 2 — важные приложения**, которые должны быть доступны бизнесу в течение нескольких часов.
   * **Категория 3 — приложения с низким приоритетом**, которые могут выдержать сбой на несколько дней.
2. Для приложений категории 1 и 2 корпорация Майкрософт рекомендует заранее спланировать, какой уровень доступа вы хотите разрешить.
   * Хотите ли вы разрешить полный доступ или ограниченный сеанс, например ограничение загрузок?
   * Хотите ли вы разрешить доступ к части приложения, но не ко всему приложению?
   * Хотите ли вы разрешить доступ информационному работнику и заблокировать доступ администратора, пока контроль доступа не будет восстановлен?
3. Для этих приложений корпорация Майкрософт также рекомендует вам спланировать, какие пути доступа вы намеренно откроете, а какие закроете.
   * Хотите ли вы разрешить лишь браузеру получать доступ и блокировать клиентов с расширенными возможностями, которые могут сохранять автономные данные?
   * Хотите ли вы разрешить доступ только для внутренних пользователей корпоративной сети и заблокировать внешних пользователей?
   * Хотите ли вы разрешить доступ из определенных стран или регионов только во время сбоев?
   * Хотите ли вы, чтобы политики для политик на случай непредвиденных обстоятельств, особенно для критически важных приложений, давали сбой или успешно выполнялись, если альтернативное управление доступом недоступно?

#### <a name="microsoft-recommendations"></a>Рекомендации корпорации Майкрософт

A contingency Conditional Access policy is a **disabled policy** that omits Azure MFA, third-party MFA, risk-based or device-based controls. Затем, когда ваша организация решит активировать план на непредвиденные случаи, администраторы могут включить эту политику и отключить обычные политики на основе контроля.

>[!IMPORTANT]
> Отключение политик, которые обеспечивают безопасность для ваших пользователей, даже временно, уменьшит ваше состояние безопасности, пока действует план на непредвиденные случаи.

* Настройте набор резервных политик, если нарушение доступа к одному типу учетных данных или одному механизму контроля доступа влияет на доступ к вашим приложениям. Настройте политику в отключенном состоянии, которая требует присоединения к домену в качестве элемента управления, в качестве резервной копии для активной политики, для которой требуется сторонний поставщик MFA.
* Снизьте риск того, что плохие субъекты будут угадывать пароли, когда не требуется MFA, следуя рекомендациям, изложенным в руководстве по паролями [здесь](https://aka.ms/passwordguidance).
* Разверните [Самостоятельный сброс пароля (SSPR) в Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) и [Защита паролем Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy), чтобы убедиться, что пользователи не используют простой пароль и условия, которые вы запретили.
* Если не достигнут определенный уровень проверки подлинности, используйте политики, которые ограничивают доступ в рамках приложения, вместо простого возврата к полному доступу. Пример.
  * Настройте политику резервного копирования, которая отправляет утверждение ограниченного сеанса в Exchange и SharePoint.
  * Если ваша организация использует Microsoft Cloud App Security, рассмотрите возможность использования политики MCAS, и тогда MCAS разрешит доступ только для чтения, но не для передачи.
* Присвойте имена политикам, чтобы их легко было найти во время сбоя. Включите следующие элементы в название политики.
  * *Номер метки* для политики.
  * Текст для отображения этой политики только в аварийном режиме. For example: **ENABLE IN EMERGENCY**
  * *Нарушение*, к которому оно относится. For example: **During MFA Disruption**
  * *Порядковый номер* для отображения порядка активации политик.
  * *Приложения*, к которым он применяется.
  * *Элементы управления*, которые будут применены.
  * Требуемые *условия*.
  
Стандарт именования для политик на случай непредвиденных обстоятельств будет выглядеть следующим образом. 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

The following example: **Example A - Contingency CA policy to restore Access to mission-critical Collaboration Apps**, is a typical corporate contingency. В этом сценарии организации, как правило, требуется MFA для всего доступа к Exchange Online и SharePoint Online, и в этом случае нарушается работа поставщика MFA для клиента (например, Azure MFA, локальный поставщик MFA или сторонняя MFA). Эта политика устраняет сбой, предоставляя определенным целевым пользователям доступ к этим приложениям с доверенных устройств Windows только тогда, когда они обращаются к приложению из своей доверенной корпоративной сети. Она также исключит из этих ограничений учетные записи для экстренных ситуаций и основных администраторов. Затем целевые пользователи получат доступ к Exchange Online и SharePoint Online, тогда как другие пользователи по-прежнему не будут иметь доступ к приложениям из-за сбоя. В этом примере потребуется именованное расположение в сети **CorpNetwork** и группа безопасности **ContingencyAccess** с целевыми пользователями, группа с именем **CoreAdmins** с основными администраторами и группа с именем **EmergencyAccess** с учетными записями для аварийного доступа. На случай непредвиденных обстоятельств для обеспечения желаемого доступа необходимы четыре политики. 

**Пример A — политики условного доступа на случай непредвиденных обстоятельств для восстановления доступа к критически важным приложениям для совместной работы:**

* Policy 1: Require Domain Joined devices for Exchange and SharePoint
  * Name: EM001 - ENABLE IN EMERGENCY: MFA Disruption[1/4] - Exchange SharePoint - Require Hybrid Azure AD Join
  * Users and Groups: Include ContingencyAccess. исключает CoreAdmins и EmergencyAccess
  * Cloud Apps: Exchange Online and SharePoint Online
  * Conditions: Any
  * Grant Control: Require Domain Joined
  * State: Disabled
* Policy 2: Block platforms other than Windows
  * Name: EM002 - ENABLE IN EMERGENCY: MFA Disruption[2/4] - Exchange SharePoint - Block access except Windows
  * Users and Groups: Include all users. исключает CoreAdmins и EmergencyAccess
  * Cloud Apps: Exchange Online and SharePoint Online
  * Conditions: Device Platform Include All Platforms, exclude Windows
  * Grant Control: Block
  * State: Disabled
* Policy 3: Block networks other than CorpNetwork
  * Name: EM003 - ENABLE IN EMERGENCY: MFA Disruption[3/4] - Exchange SharePoint - Block access except Corporate Network
  * Users and Groups: Include all users. исключает CoreAdmins и EmergencyAccess
  * Cloud Apps: Exchange Online and SharePoint Online
  * Conditions: Locations Include any location, exclude CorpNetwork
  * Grant Control: Block
  * State: Disabled
* Policy 4: Block EAS Explicitly
  * Name: EM004 - ENABLE IN EMERGENCY: MFA Disruption[4/4] - Exchange - Block EAS for all users
  * Users and Groups: Include all users
  * Cloud Apps: Include Exchange Online
  * Conditions: Client apps: Exchange Active Sync
  * Grant Control: Block
  * State: Disabled

Порядок активации:

1. Исключите ContingencyAccess, CoreAdmins и EmergencyAccess из существующей политики MFA. Убедитесь в том, что пользователь в ContingencyAccess может получить доступ к SharePoint Online и Exchange Online.
2. Enable Policy 1: Verify users on Domain Joined devices who are not in the exclude groups are able to access Exchange Online and SharePoint Online. Убедитесь в том, что пользователи группы "Исключить" могут получить доступ к SharePoint Online и Exchange с любого устройства.
3. Enable Policy 2: Verify users who are not in the exclude group cannot get to SharePoint Online and Exchange Online from their mobile devices. Убедитесь в том, что пользователи группы "Исключить" могут получить доступ к SharePoint и Exchange с любого устройства (Windows/iOS/Android).
4. Enable Policy 3: Verify users who are not in the exclude groups cannot access SharePoint and Exchange off the corporate network, even with a domain joined machine. Убедитесь в том, что пользователи группы "Исключить" могут получить доступ к SharePoint и Exchange с любой сети.
5. Enable Policy 4: Verify all users cannot get Exchange Online from the native mail applications on mobile devices.
6. Отключите существующую политику MFA для SharePoint Online и Exchange Online.

В этом следующем примере **пример Б — политики условного доступа на случай непредвиденных обстоятельств, которые разрешают мобильный доступ к Salesforce** , восстанавливается доступ бизнес-приложения. В этом сценарии клиент обычно требует, чтобы его сотрудники отдела продаж имели доступ к Salesforce (настроенный для единого входа в Azure AD) с мобильных устройств, чтобы он был разрешен только со совместимых устройств. В этом случае сбой заключается в существовании проблемы с оценкой соответствия устройств и происходит в трудное время, когда отдел по продажам должен иметь доступ к Salesforce, чтобы закрыть сделки. Эти политики на случай непредвиденных обстоятельств предоставят доступ к Salesforce с мобильного устройства критическим пользователям, чтобы те могли продолжать закрывать сделки и не нарушать работу. В этом примере **SalesforceContingency** содержит всех сотрудников отдела продаж, которым необходимо сохранить доступ, а **SalesAdmins** — необходимых администраторов Salesforce.

**Пример Б — политики условного доступа на случай непредвиденных обстоятельств:**

* Policy 1: Block everyone not in the SalesContingency team
  * Name: EM001 - ENABLE IN EMERGENCY: Device Compliance Disruption[1/2] - Salesforce - Block All users except SalesforceContingency
  * Users and Groups: Include all users. исключает SalesAdmins и SalesforceContingency
  * Cloud Apps: Salesforce.
  * Conditions: None
  * Grant Control: Block
  * State: Disabled
* Policy 2: Block the Sales team from any platform other than mobile (to reduce surface area of attack)
  * Name: EM002 - ENABLE IN EMERGENCY: Device Compliance Disruption[2/2] - Salesforce - Block All platforms except iOS and Android
  * Users and Groups: Include SalesforceContingency. исключает SalesAdmins
  * Cloud Apps: Salesforce
  * Conditions: Device Platform Include All Platforms, exclude iOS and Android
  * Grant Control: Block
  * State: Disabled

Порядок активации:

1. Исключите SalesAdmins и SalesforceContingency из существующей политики соответствия устройств для Salesforce. Убедитесь в том, что пользователь группы SalesforceContingency имеет доступ к Salesforce.
2. Enable Policy 1: Verify users outside of SalesContingency cannot access Salesforce. Убедитесь в том, что пользователи SalesAdmins и SalesforceContingency могут получить доступ к Salesforce.
3. Enable Policy 2: Verify users in the SalesContingency group cannot access Salesforce from their Windows/Mac laptops but can still access from their mobile devices. Убедитесь в том, что SalesAdmin может получить доступ к Salesforce с любого устройства.
4. Отключите существующую политику соответствия устройств для Salesforce.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Развертывание синхронизации хэша пароля, даже если вы федеративны или используете сквозную проверку подлинности

Блокировка пользователя также может произойти, если выполняются следующие условия.

- Ваша организация использует гибридное решение для идентификации со сквозной проверкой подлинности или федерацией.
- Ваши локальные системы идентификации (например, Active Directory, AD FS или зависимый компонент) недоступны. 
 
Для обеспечения большей устойчивости вашей организации следует [включить синхронизацию хэша пароля](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn), поскольку она позволяет вам [переключиться на использование синхронизации хэша пароля](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin), если локальные системы идентификации не работают.

#### <a name="microsoft-recommendations"></a>Рекомендации корпорации Майкрософт
 Включите синхронизацию хэша пароля с помощью мастера Azure AD Connect, независимо от того, использует ваша организация федерацию или сквозную проверку подлинности.

>[!IMPORTANT]
> Для использования синхронизации хэшей паролей не нужно преобразовывать пользователей из федеративной в управляемую проверку подлинности.

## <a name="during-a-disruption"></a>Во время сбоя

Если вы выбрали реализацию плана по устранению рисков, то сможете автоматически пережить один сбой управления доступом. Однако, если вы решили создать план на непредвиденные случаи, то во время сбоя управления доступом вы сможете активировать свои политики на случай непредвиденных обстоятельств.

1. Включите ваши политики на случай непредвиденных обстоятельств, которые предоставляют целевым пользователям доступ к определенным приложениям из определенных сетей.
2. Отключите свои обычные политики на основе контроля.

### <a name="microsoft-recommendations"></a>Рекомендации корпорации Майкрософт

В зависимости от того, какие меры по устранению рисков или непредвиденные обстоятельства используются во время сбоя, ваша организация может предоставлять доступ только с помощью паролей. Отсутствие мер безопасности представляет собой значительный риск для безопасности, который необходимо тщательно взвесить. Организациям необходимо:

1. Документировать каждое изменение и предыдущее состояние (как часть вашей стратегии управления изменениями), чтобы иметь возможность откатывать любые непредвиденные обстоятельства, которые вы внедрили, как только управление доступом будет полностью рабочим.
2. Предполагать, что злоумышленники будут пытаться собрать пароли с помощью атаки путем распыления пароля или фишинговых атак, пока отключена MFA. Кроме того, у плохих субъектов уже могут быть пароли, которые ранее не давали доступ ни к какому ресурсу, который может выполняться в этом окне. Для критически важных пользователей, таких как руководители, вы можете частично снизить этот риск, сбросив перед отключением MFA их пароли.
3. Архивировать все действия по входу в систему, чтобы определить, кто к чему имеет доступ при отключенной MFA.
4. [Triage all risk detections reported](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) during this window.

## <a name="after-a-disruption"></a>После сбоя

Отмените изменения, внесенные вами как часть активированного плана на непредвиденные случаи, после восстановления службы, которая вызвала сбой. 

1. Включите обычные политики.
2. Отключите политики на случай непредвиденных обстоятельств. 
3. Откатите любые другие изменения, которые вы сделали и задокументировали во время сбоя.
4. Если вы использовали учетную запись для аварийного доступа, не забудьте повторно создать учетные данные и физически защитить детали новых учетных данных как часть процедур учетной записи для аварийного доступа.
5. Continue to [triage all risk detections reported](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) after the disruption for suspicious activity.
6. Отмените все токены обновления, выпущенные [с помощью PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) для целевого набора пользователей. Отмена всех токенов обновления важна для привилегированных учетных записей, используемых во время сбоя. Это заставит их повторно пройти проверку подлинности и получить контроль над восстановленными политиками.

## <a name="emergency-options"></a>Параметры аварийного режима

 In case of an emergency and your organization did not previously implement a mitigation or contingency plan, then follow the recommendations in the [Contingencies for user lockout](#contingencies-for-user-lockout) section if they already use Conditional Access policies to enforce MFA.
Если ваша организация использует устаревшие политики MFA для каждого пользователя, вы можете рассмотреть следующую альтернативу:

1. Если у вас есть исходящий IP-адрес корпоративной сети, вы можете добавить его в качестве доверенных IP-адресов, чтобы включить проверку подлинности только в корпоративной сети.
   1. Если у вас нет перечня исходящих IP-адресов или вам нужно включить доступ внутри и за пределами корпоративной сети, вы можете добавить все адресное пространство IPv4 в качестве доверенных IP-адресов, указав 0.0.0.0/1 и 128.0.0.0/1.

>[!IMPORTANT]
 > If you broaden the trusted IP addresses to unblock access, risk detections associated with IP addresses (for example, impossible travel or unfamiliar locations) will not be generated.

>[!NOTE]
 > Настройка [доверенных IP-адресов](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings) для MFA Azure доступна только с [лицензиями Azure AD Premium](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing).

## <a name="learn-more"></a>Дополнительные сведения

* [Настройка сервера Многофакторной идентификации Azure для веб-приложений IIS](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [Управление учетными записями администратора для аварийного доступа в Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [Быстрое начало. Настройка именованных расположений в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
  * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [Как планировать гибридную реализацию присоединения к Azure Active Directory](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Руководство по развертыванию Windows Hello для бизнеса](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Password Guidance - Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf) (Руководство о паролях — Microsoft Research)
* [What are conditions in Azure Active Directory Conditional Access?](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [What are access controls in Azure Active Directory Conditional Access?](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
