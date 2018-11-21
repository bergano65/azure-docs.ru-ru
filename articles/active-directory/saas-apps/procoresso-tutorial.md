---
title: Руководство. Интеграция Azure Active Directory с Procore SSO | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Procore SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 9460f08c3ff73c70b9b78235c21ee7ed84e016d7
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684517"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Руководство. Интеграция Azure Active Directory с Procore SSO

В этом руководстве описано, как интегрировать Procore SSO с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Procore SSO обеспечивает следующие преимущества:

- С помощью AAD вы можете контролировать доступ к Procore SSO.
- Вы можете включить автоматический вход пользователей в Procore SSO (единый вход) с помощью учетных записей AAD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Procore SSO, вам потребуется:

- подписка Azure AD;
- подписка на Procore SSO с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Procore SSO из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-procore-sso-from-the-gallery"></a>Добавление Procore SSO из коллекции

Чтобы настроить интеграцию Procore SSO с Azure AD, необходимо добавить Procore SSO из коллекции в список управляемых приложений SaaS.

**Чтобы добавить приложение Procore SSO из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Procore SSO**, выберите **Procore SSO** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Procore SSO в списке результатов](./media/procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Procore SSO с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Procore SSO соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Procore SSO.

Чтобы настроить и проверить единый вход Azure AD в Procore SSO, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Procore SSO](#creating-a-procore-sso-test-user)** требуется для того, чтобы в Procore SSO существовал пользователь Britta Simon, связанный с одноименным пользователем в AAD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход в AAD на портале Azure и настроить его в приложении Procore SSO.

**Чтобы настроить единый вход Azure AD в Procore SSO, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Procore SSO** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Выбрать метод единого входа** щелкните **Выбрать** для режима **SAML**, чтобы включить единый вход.

    ![Настройка единого входа](common/tutorial_general_301.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Настройка единого входа](common/editconfigure.png)

4. В разделе **Базовая конфигурация SAML** не нужно выполнять никаких действий, так как приложение уже предварительно интегрировано с Azure.

    ![Сведения о домене и URL-адресах единого входа для приложения Procore SSO](./media/procoresso-tutorial/tutorial_procoresso_url.png)

5. На странице **сертификата подписи SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать **XML-файл метаданных федерации**, а затем сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](./media/procoresso-tutorial/tutorial_procoresso_certificate.png) 

6. Из раздела **настройки Procore SSO** скопируйте требуемый URL-адрес.

    a. URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода

    ![Настройка Procore SSO](common/configuresection.png)

7. Чтобы настроить единый вход на стороне **Procore SSO**, выполните вход на корпоративный сайт Procore с правами администратора.

8. В раскрывающемся списке инструментов щелкните **Admin** (Администрирование), чтобы открыть страницу для настройки единого входа.

    ![Настройка единого входа](./media/procoresso-tutorial/procore_tool_admin.png)

9. Вставьте в поля нужные значения, как описано ниже

    ![Настройка единого входа](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. В текстовое поле **URL-адреса издателя единого входа** вставьте значение **идентификатора AAD**, скопированное на портале Azure.

    b. В поле **целевого URL-адреса входа SAML** вставьте значение **URL-адреса входа**, скопированное на портале Azure.

    c. Теперь откройте **XML-файл метаданных федерации**, который вы ранее скачали с портала Azure, и скопируйте из него сертификат, расположенный в теге с именем **X509Certificate**. Вставьте это значение в поле **Single Sign On x509 Certificate** (Сертификат x509 для единого входа).

10. Щелкните **Save Changes** (Сохранить изменения).

11. Когда вы завершите эту настройку, передайте **имя домена** (например, **contoso.com**), для которого осуществляется вход в Procore, в [службу поддержки Procore](https://support.procore.com/) для активации федеративного единого входа для этого домена.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Создание пользователя Azure AD][100]

2. В верхней части экрана выберите **Новый пользователь**.

    ![Создание тестового пользователя Azure AD](common/create_aaduser_01.png) 

3. В разделе свойств пользователя сделайте следующее.

    ![Создание тестового пользователя Azure AD](common/create_aaduser_02.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например, BrittaSimon@contoso.com

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="creating-a-procore-sso-test-user"></a>Создание тестового пользователя Procore SSO

Выполните следующие действия, чтобы создать тестового пользователя Procore на стороне Procore SSO.

1. Войдите на корпоративный сайт Procore с правами администратора.  

2. В раскрывающемся списке инструментов щелкните **Directory** (Каталог), чтобы открыть страницу корпоративного каталога.

    ![Настройка единого входа](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Щелкните **Add a Person** (Добавить сотрудника), чтобы открыть форму регистрации, и введите следующие параметры.

    ![Настройка единого входа](./media/procoresso-tutorial/Procore_user_add.png)

    a. В текстовое поле **First Name** (Имя) введите имя пользователя, например **Britta**.

    b. В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя, например **Simon**.

    c. В текстовое поле **Email Address** (Адрес электронной почты) введите адрес электронной почты пользователя, например **BrittaSimon@contoso.com**.

    d. Для параметра **Permission Template** (Шаблон разрешений) выберите значение **Apply Permission Template Later** (Применить шаблон разрешений позже).

    д. Нажмите кнопку **Создать**.

4. Проверьте и измените при необходимости сведения о добавляемом сотруднике.

    ![Настройка единого входа](./media/procoresso-tutorial/Procore_user_check.png)

5. Щелкните действие **Save and Send Invitiation** (Сохранить и отправить приглашение), если требуется приглашение, или просто **Save** (Сохранить), чтобы завершить регистрацию пользователя.
    
    ![Настройка единого входа](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Procore SSO.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201]

2. В списке приложений выберите **Procore SSO**.

    ![Настройка единого входа](./media/procoresso-tutorial/tutorial_procoresso_app.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Procore SSO на панели доступа, вы автоматически войдете в приложение Procore SSO.
Дополнительные сведения о панели доступа см. в статье с [общими сведениями о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
