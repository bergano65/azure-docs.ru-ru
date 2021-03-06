---
title: Управление интересами для Dynamics 365 для сотрудничества с клиентами | Azure Marketplace
description: Настройка управления интересами для Dynamics 365 для участия клиентов.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 37cf613b6e0bd2ec9910dd3e7431c0feaa02431c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812310"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>Настройка управления интересами для Dynamics 365 для участия клиентов

В этой статье описывается, как настроить Dynamics 365 для взаимодействия с клиентами (ранее Dynamics CRM Online). Дополнительные сведения об изменениях см. [здесь](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) . 

>[!Note]
>Эти инструкции относятся только к размещенной облачной среде Microsoft Dynamics 365 для среды взаимодействия клиентов. Прямое подключение к локальной среде Dynamics в настоящее время не поддерживается. Существуют и другие варианты получения интересов, таких как настройка [конечной точки HTTPS](./commercial-marketplace-lead-management-instructions-https.md) или [таблицы Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) для получения интересов.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения действий, описанных в этой статье, необходимы следующие разрешения пользователя.

* Чтобы иметь возможность установить решение и выполнить эти инструкции, необходимо быть администратором в Dynamics 365 для экземпляра служб взаимодействия с клиентами.
* Необходимо быть администратором клиента, чтобы создать новую учетную запись службы для службы интереса, которая будет использоваться для отправки интересов из предложений Marketplace.
* Необходимо иметь доступ к порталу администрирования Office 365.
* Необходимо иметь доступ к портал Azure.

## <a name="install-the-solution"></a>Установка решения

1.  Скачайте [Microsoft Marketplace решение для записи интересов](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) и сохраните его локально на своем компьютере.

2.  Откройте Dynamics 365 для участия клиентов, перейдя по URL-адресу экземпляра Dynamics (например, `https://tenant.crm.dynamics.com`).

3.  Параметры доступа, щелкнув значок шестеренки и **Дополнительные параметры** на верхней панели навигации.
 
    ![Dynamics — дополнительные параметры](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  На странице Параметры откройте меню Параметры на верхней панели навигации и выберите **решения**.

    >[!Note]
    >Если вы не видите параметры на следующем снимке экрана, у вас нет необходимых разрешений для продолжения. Обратитесь к администратору в Dynamics 365 для экземпляра служб взаимодействия с клиентами.

    ![Dynamics 365 — решения](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. На странице решения выберите **Импорт** и перейдите к месту, где вы сохранили Microsoft Marketplace решение для *записи интересов* , скачанное на шаге 1.

    ![Dynamics 365 для участия клиентов — импорт](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. Завершите импорт решения, следуя мастеру импорта решений.

## <a name="configure-user-permissions"></a>Настройка разрешений пользователя

Для записи интересов в экземпляр Dynamics 365 для служб взаимодействия с клиентами необходимо предоставить учетной записи службы общий доступ и настроить разрешения для учетной записи.

Чтобы создать учетную запись службы и назначить ей разрешения, выполните описанные ниже действия. Можно использовать **Azure Active Directory** или **Office 365**.

>[!Note]
>В зависимости от выбранного варианта проверки подлинности можно перейти к соответствующим инструкциям в зависимости от вашего выбора. См. статью [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) или [Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Мы рекомендуем использовать этот вариант, так как вы получаете дополнительное преимущество никогда не нуждается в обновлении имени пользователя и пароля для получения интересов. Чтобы использовать параметр Azure Active Directory, укажите идентификатор приложения, ключ приложения и идентификатор каталога из приложения Active Directory.

Чтобы настроить Azure Active Directory для Dynamics 365 для служб поддержки клиентов, выполните следующие действия.

1. Войдите в [портал Azure](https://portal.azure.com/), а затем выберите службу Azure Active Directory в области навигации слева.

2. Выберите **Свойства** в Azure Active Directory левой навигационной панели и СКОПИРУЙТЕ значение **идентификатора каталога** на этой странице. Сохраните это значение, так как это значение *идентификатора каталога* , которое необходимо указать на портале публикации для получения интересов для вашего предложения Marketplace.

    ![Azure Active Directory-свойства](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. Выберите **Регистрация приложений** в области навигации слева Azure Active Directory, а затем щелкните **Новая регистрация** на этой странице.
4. Введите имя приложения. Укажите понятное имя приложения.
5. В разделе Поддерживаемые типы учетных записей выберите **учетные записи в любом организационном каталоге**.
6. В разделе URI перенаправления выберите **веб** и укажите URI (например, `https://contosoapp1/auth`). 
7. Выберите **Зарегистрировать**.

    ![Регистрация приложения](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. Теперь, когда приложение зарегистрировано, обратитесь к странице обзора приложения и скопируйте значение **идентификатора приложения (клиента)** на этой странице. Сохраните это значение, так как это значение *идентификатора приложения (клиента)* , которое необходимо указать на портале публикации и в Dynamics для получения интересов для вашего предложения Marketplace.

    ![Идентификатор приложения (клиента)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. Выберите **Сертификаты и секреты** в левой области навигации приложения и на этой странице выберите **новый секрет клиента** . Введите понятное описание секрета клиента и выберите параметр **никогда** в разделе истечение срока действия. Нажмите кнопку **Добавить** , чтобы создать секрет клиента.

    ![Приложение — сертификация и секретные данные](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. После успешного создания секрета клиента **скопируйте значение секрета клиента**. Вы не сможете получить значение после выхода из страницы. Сохраните это значение, так как это значение *секрета клиента* , которое необходимо указать на портале публикации для получения интересов для вашего предложения Marketplace. 
11. Выберите **разрешения API** в левой области навигации приложения, а затем выберите **Добавить разрешение**.
12. Выберите API Майкрософт, а затем выберите **Dynamics CRM** в качестве API.
13. В разделе *какой тип разрешений требуется приложению*, убедитесь, что выбраны **делегированные разрешения** . Проверьте разрешение **user_impersonation** *доступом Common Data Service в качестве пользователей организации*. Выберите **Добавить разрешения**.

    ![Добавление разрешений](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. После выполнения шагов 1-13 на портал Azure перейдите к своему экземпляру Dynamics 365 для взаимодействия с клиентами, перейдя по URL-адресу (например, `https://tenant.crm.dynamics.com`).
15. Параметры доступа, щелкнув значок шестеренки и **Дополнительные параметры** на верхней панели навигации.
16. На странице Параметры откройте меню Параметры на верхней панели навигации и выберите **Безопасность**.
17. На странице Безопасность выберите **Пользователи**.  На странице пользователи выберите раскрывающийся список "включенные пользователи", чтобы переключиться на **пользователей приложений**.
18. Нажмите **Создать**, чтобы создать пользователя. 

    ![Создание пользователя](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. В поле **Новый пользователь**убедитесь, что выбран параметр Пользователь: пользователь приложения. Укажите имя пользователя, полное имя и адрес электронной почты, которые будут использоваться для этого подключения. Кроме того, вставьте **идентификатор приложения** , созданного на портал Azure из шага 8. Нажмите кнопку **сохранить и закрыть** , чтобы завершить добавление пользователя.

    ![Новый пользователь](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. Перейдите в раздел "Параметры безопасности" этой статьи и завершите настройку подключения для этого пользователя.

### <a name="office-365"></a>Office 365

Если вы не хотите использовать Azure Active Directory, можно зарегистрировать нового пользователя в *центре администрирования Microsoft 365*. Вам нужно будет обновлять свое имя пользователя и пароль каждые 90 дней, чтобы продолжать получать данные о потенциальных клиентах.

Чтобы настроить Office 365 для Dynamics 365 для служб поддержки клиентов, выполните следующие действия.

1. Войдите в [центр администрирования Microsoft 365](https://admin.microsoft.com).

2. Нажмите **Добавить пользователя**.

    ![Центр администрирования Microsoft 365. Добавление пользователя](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. Создайте пользователя для службы модуля записи интересов. Настройте следующие параметры:

    * Укажите имя пользователя
    * Укажите пароль и снимите флажок "сделать этот пользователь сменой пароля при первом входе в систему".
    * Укажите для пользователя роль "Пользователь (без прав администратора)".
    * Выберите "Dynamics 365. план взаимодействия с клиентами" в качестве лицензии продукта, показанной на следующем снимке экрана. За выбранную лицензию будет начисляться плата. 

Сохраните эти значения, так как это значения *имени пользователя и пароля* , которые необходимо указать на портале публикации для получения интересов для вашего предложения Marketplace.

![Центр администрирования Microsoft 365 — новый пользователь](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Параметры безопасности

На последнем шаге нужно разрешить созданному пользователю записывать данные о потенциальных клиентах.

1. Откройте Dynamics 365 для участия клиентов, перейдя по URL-адресу экземпляра Dynamics (например, `https://tenant.crm.dynamics.com`).
2. Параметры доступа, щелкнув значок шестеренки и **Дополнительные параметры** на верхней панели навигации.
3. На странице Параметры откройте меню Параметры на верхней панели навигации и выберите **Безопасность**.
4. На странице Безопасность выберите **Пользователи** и выберите пользователя, созданного в разделе Настройка разрешений пользователя этого документа, а затем выберите **Управление ролями**. 

    ![управлять ролями;](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. Найдите имя роли "Microsoft Marketplaceный модуль записи интересов" и выберите его, чтобы назначить пользователю роль.

    ![Управление ролями пользователей](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >Эту роль создало импортированное решение, и ей предоставлены разрешения только на запись данных о потенциальных клиентах и на отслеживание версии решения для обеспечения совместимости.

6. Вернитесь на страницу Безопасность и выберите **роли безопасности**. Выполните поиск роли "Microsoft Marketplaceный модуль записи интересов" и выберите ее.

    ![Роли безопасности](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. В роли безопасности перейдите на вкладку **основные записи** . Найдите сущность "параметры пользовательского интерфейса сущности пользователя" и включите разрешения Создание, чтение и запись для пользователя (1/4 желтого круга) для этой сущности, щелкнув один раз в каждом из соответствующих кругов.

    ![Microsoft Marketplace модуль записи интересов — основные записи](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. Теперь перейдите на вкладку " **Настройка** ". Найдите Tor в виде сущности "Системное задание" и дополните разрешения на чтение, запись и Аппендто в организация (сплошная зеленая) для этой сущности, щелкнув четыре раза в каждом из соответствующих кругов.

    ![Microsoft Marketplace модуль записи интересов — Настройка](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **Сохраните и закройте**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>Настройка предложения по отправке интересов в Dynamics 365 для участия клиентов

Когда вы будете готовы к настройке сведений об управлении интересами для вашего предложения на портале публикации, выполните следующие действия.

1. Перейдите на страницу **настройки предложения** для вашего предложения.
2. Выберите **Подключиться** в разделе Управление интересами.

    ![Подключение к управлению интересами](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. Во всплывающем окне "сведения о подключении" выберите **Dynamics 365 для** взаимодействия с клиентом для назначения интереса.

    ![Сведения о подключении — назначение интереса](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. Укажите **URL-адрес экземпляра Dynamics 365** , например `https://contoso.crm4.dynamics.com`.
5. Выберите метод **проверки подлинности**, Azure Active Directory или Office 365. 
6. Если вы выбрали Azure Active Directory, укажите **идентификатор приложения** (например: `23456052-aaaa-bbbb-8662-1234df56788f`), **идентификатор каталога** (например: `12345678-8af1-4asf-1234-12234d01db47`) и **секрет клиента** (пример: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`).

    ![Сведения о подключении — Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Если вы выбрали Office 365, укажите **имя пользователя** (например: `contoso@contoso.onmicrosoft.com`) и пароль (пример: `P@ssw0rd`).

    ![Сведения о подключении — имя пользователя](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

>[!Note]
>Необходимо завершить настройку остальной части предложения и опубликовать его, прежде чем вы сможете получить интересы для предложения.
