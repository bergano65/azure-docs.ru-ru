---
title: Пять шагов по интеграции всех приложений с Azure AD
description: В этом руководство объясняется, как интегрировать все приложения с Azure AD. На каждом шаге мы объясним значение и предоставляем ссылки на ресурсы, которые покажут технические подробности.
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: c75d04faf9ac59f21aad1dcd88dfe83699a11941
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057378"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>Пять шагов по интеграции всех приложений с Azure AD

Azure Active Directory (Azure AD) — это облачная служба управления удостоверениями и доступом Майкрософт. Azure AD предоставляет безопасные решения для проверки подлинности и авторизации, чтобы клиенты, партнеры и сотрудники могли получить доступ к необходимым приложениям. В Azure AD, [Условный доступ](https://docs.microsoft.com/azure/active-directory/conditional-access/overview), [многофакторная идентификация](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks), [единый вход](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)и [Автоматическая подготовка пользователей](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) обеспечивают простоту и безопасность управления удостоверениями и доступом.

Если у вашей компании есть подписка Microsoft 365, скорее всего, вы [уже используете](https://docs.microsoft.com/office365/enterprise/about-office-365-identity) Azure AD. Тем не менее Azure AD можно использовать для всех приложений, а благодаря [централизации управления приложениями](https://docs.microsoft.com/azure/active-directory/manage-apps/common-scenarios) вы можете использовать одни и те же функции управления удостоверениями, средства и политики для всего портфеля приложений. Это обеспечит единое решение, повышающее безопасность, уменьшая затраты, повышает производительность и позволяет обеспечить соответствие требованиям. И вы получите удаленный доступ к локальным приложениям.

В этом руководство объясняется, как интегрировать все приложения с Azure AD. На каждом шаге мы объясним значение и предоставляем ссылки на ресурсы, которые покажут технические подробности. Мы предлагаем эти шаги в том порядке, в котором мы рекомендуем. Однако можно перейти к любой части процесса, чтобы приступить к работе, чтобы добавить наибольшее значение.

Другие ресурсы по этой теме, в том числе технические документы по бизнес-процессам, которые можно найти в наших [ресурсах для переноса приложений на Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) странице.

## <a name="1-use-azure-ad-for-new-applications"></a>1. Использование Azure AD для новых приложений

Сначала Сосредоточьтесь на новых приобретенных приложениях. Когда ваш бизнес начинает использовать новое приложение, [добавьте его в свой клиент Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) прямо сейчас. Настройте политику компании, чтобы Добавление новых приложений в Azure AD было стандартным подходом в вашей организации. Это минимально нарушает существующие бизнес-процессы и позволяет исследовать и доказать ценность, получаемую от интеграции приложений, не изменяя способ ведения бизнеса в вашей среде.

В Azure Active Directory (Azure AD) имеется коллекция, которая содержит тысячи предварительно интегрированных приложений, которые упрощают начало работы. Вы можете [Добавить приложение коллекции в свою организацию Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) с помощью пошаговых [руководств](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) по интеграции с популярными приложениями, такими как:

- [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-tutorial)
- [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)
- [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-tutorial)
- [AWS](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial)
- [Slack](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial)

Кроме того, можно [интегрировать приложения, не входящие в коллекцию](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app), включая все приложения, которые уже существуют в вашей организации, или любое стороннее приложение от поставщика, который еще не является частью коллекции Azure AD. Вы также можете [Добавить приложение в коллекцию,](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-app-gallery-listing) если оно там отсутствует.

Наконец, вы также можете интегрировать приложения, которые вы разрабатываете самостоятельно. Это описано на шаге 5 этого руководств.

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. определение использования существующего приложения и определение приоритета работы

Затем вы узнаете о том, как часто используются сотрудники приложений, и определите приоритеты работы для их интеграции с Azure AD.

Вы можете начать с использования [средств облачного обнаружения](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) Microsoft Cloud App Security&#39;s для обнаружения и управления &quot; теневыми &quot; ИТ в вашей сети (т. е. приложениями, не управляемыми ИТ-отделом). Для упрощения и расширения процесса обнаружения можно [использовать Advanced Threat protection (ATP) защитника Майкрософт](https://docs.microsoft.com/cloud-app-security/wdatp-integration) .

Кроме того, вы можете использовать [отчет о действиях AD FS приложений](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) в портал Azure, чтобы обнаружить все AD FSные приложения в Организации, число уникальных пользователей, выполнивших вход в них, и совместимость для интеграции их с Azure AD.

После того как вы обнаружите имеющуюся альбомную ориентацию, вам потребуется [создать план](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) и определить приоритеты для интеграции приложений с наивысшим приоритетом. Ниже приведены некоторые примеры вопросов, которые можно задать для этого процесса:

- Какие приложения наиболее часто используются?
- Что такое угрожаемый?
- Какие приложения будут списаны в будущем, что сделает перемещение ненужным?
- Какие приложения должны оставаться в локальной среде и не могут быть перемещены в облако?

После интеграции всех приложений вы увидите самые большие преимущества и экономию затрат, и вы больше не будете полагаться на несколько решений по идентификации. Тем не менее вы сможете упростить управление удостоверениями и повысить уровень безопасности при перемещении ступенчатый в сторону этой цели. Вы хотите использовать это время для определения приоритета работы и принятия решения о том, что имеет смысл в конкретной ситуации.

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. Интеграция приложений, зависящих от других поставщиков удостоверений

В процессе обнаружения вы могли найти приложения, которые не отправляются ИТ-отделом, что оставляет ваши данные и ресурсы уязвимыми. У вас также могут быть приложения, использующие альтернативные решения идентификации, в том числе службы федерации Active Directory (AD FS) (ADFS) или другие поставщики удостоверений. Рассмотрите способ консолидации управления удостоверениями и доступом, чтобы сэкономить деньги и повысить уровень безопасности. Уменьшение числа решений по идентификации вы сможете:

- Изменяйте деньги, устраняя необходимость в локальной подготовке пользователей и проверке подлинности, а также о платных лицензиях, оплаченных другими поставщиками удостоверений облака для той же службы.
- Сократите административные издержки и обеспечьте более строгую защиту с меньшим количеством избыточностей в процессе управления удостоверениями и доступом.
- Предоставление сотрудникам возможности безопасного единого входа для доступа ко всем необходимым приложениям через [портал MyApps](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).
- Повысьте эффективность анализа служб Azure AD&#39;, связанных с [защитой идентификации](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) , таких как условный доступ, увеличив объем данных, которые он получает от использования вашего приложения, и расширьте свои преимущества до новых приложений.

Мы опубликовали руководство по управлению бизнес-процессом интеграции приложений с Azure AD, включая [афишу](https://aka.ms/AppOnePager) и [презентацию](https://aka.ms/AppGuideline) , которые вы можете использовать для предоставления владельцам и заинтересованным потребностям владельцев бизнеса и приложений. Вы можете изменить эти образцы с помощью собственной фирменной символики и опубликовать их в своей организации на корпоративном портале, информационном бюллетене или на другом носителе по мере завершения этого процесса.

Лучше всего начать с оценки использования службы федерации Active Directory (AD FS) (ADFS). Многие организации используют ADFS для проверки подлинности с помощью приложений SaaS, настраиваемых бизнес-приложений, а также приложений Office 365 и Azure AD.

![На схеме показаны локальные приложения, бизнес-приложения, приложения SaaS и, через Azure AD, Office 365 все подключаются с помощью пунктирных линий в Active Directory и AD FS.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

Эту конфигурацию можно обновить, [заменив ADFS на Azure AD в качестве центра](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure) вашего решения для управления удостоверениями. Это обеспечивает вход для всех приложений, к которым необходимо получить доступ, и позволяет сотрудникам легко находить любые бизнес-приложения, необходимые им на [портале MyApps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access), помимо других преимуществ, упомянутых выше.

![На схеме показаны локальные приложения с помощью Active Directory и AD FS, бизнес-приложений, приложений SaaS и Office 365, которые подключаются с помощью пунктирных линий в Azure Active Directory.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

После того как Azure AD станет центральным поставщиком удостоверений, вы сможете полностью переключиться с ADFS, а не использовать федеративное решение. Приложения, ранее использовавшие ADFS для проверки подлинности, теперь могут использовать только Azure AD.

![На схеме показаны локальные, бизнес-приложения, приложения SaaS и Office 365, которые подключаются к Azure Active Directory с помощью пунктирных линий. Active Directory и AD FS отсутствуют.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

Вы также можете перенести приложения, использующие другой облачный поставщик удостоверений, в Azure AD. В Организации может существовать несколько решений управления доступом для идентификации (IAM). Переход на одну инфраструктуру Azure AD — это возможность снизить зависимости от связанных с IAM-лицензиями (локально или в облаке) и затрат на инфраструктуру. В тех случаях, когда вы уже платите за Azure AD через лицензии M365, нет необходимости платить дополнительные затраты на другое решение IAM.

## <a name="4-integrate-on-premises-applications"></a>4. интеграция локальных приложений

Традиционно приложения поддерживали защиту, разрешая доступ только при подключении к корпоративной сети. Однако в постоянно подключенном мире мы хотим предоставить доступ к приложениям клиентам, партнерам и сотрудникам независимо от того, где они находятся в мире. [Azure AD application proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) (апппрокси) — это функция Azure AD, которая подключает существующие локальные приложения к Azure AD и не требует поддержки пограничных серверов или другой дополнительной инфраструктуры.

![На схеме показана служба прокси приложения в действии. Пользователь обращается к " https://sales.contoso.com ", и их запрос перенаправляется через " https://sales-contoso.msappproxy.net " в Azure Active Directory на локальный адрес " http://sales ".](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

Вы можете использовать [учебник. Добавьте локальное приложение для удаленного доступа через прокси приложения в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application) , чтобы включить прокси приложения и добавить локальное приложение в клиент Azure AD.

Кроме того, можно интегрировать контроллеры доставки приложений, например F5 BIG-IP APM или Zscaler Private Access. Интеграция этих служб с Azure AD позволяет получить современные средства проверки подлинности и управления удостоверениями Azure AD наряду с функциями управления трафиком и безопасности продукта партнера. Мы вызываем это решение [безопасный гибридный доступ](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access). Если вы используете любую из следующих служб на сегодняшний день, у нас есть учебники, которые помогут вам выполнить интеграцию с Azure AD.

- [Доступ к корпоративному приложению Akamai (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)
- [Контроллер доставки приложений Citrix](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial) (ранее назывался Citrix NetScaler)
- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)
- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)

## <a name="5-integrate-apps-your-developers-build"></a>5. Интегрируйте приложения, создаваемые разработчиками

Для приложений, созданных в вашей компании, разработчики могут использовать [платформу Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/) для реализации проверки подлинности и авторизации. Приложения, интегрированные с платформой, [регистрируются в Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app) и управляются так же, как и любые другие приложения в портфеле.

Разработчики могут использовать платформу как для внутренних приложений, так и для приложений, ориентированных на пользователей, и существуют и другие преимущества использования платформы. [Библиотеки проверки подлинности Microsoft (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview), являющиеся частью платформы, позволяют разработчикам включать современные возможности, такие как многофакторная проверка подлинности, и использование ключей безопасности для доступа к приложениям без необходимости их реализации. Кроме того, приложения, интегрированные с платформой Microsoft Identity, могут получать доступ к [Microsoft Graph](https://docs.microsoft.com/azure/active-directory/develop/microsoft-graph-intro) — единой КОНЕЧНОЙ точке API, предоставляющей Microsoft 365 данные, описывающие закономерности производительности, идентификации и безопасности в Организации. Разработчики могут использовать эти сведения для реализации функций, повышающих производительность пользователей. Например, определив людей, которые пользователь взаимодействовал с недавно и отображая их в пользовательском интерфейсе приложения&#39;s.

[Серия видеороликов](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX) содержит исчерпывающие сведения о платформе, а также [множество примеров кода](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) на поддерживаемых языках и платформах.

## <a name="next-steps"></a>Дальнейшие действия

- [Ресурсы для переноса приложений в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)
