---
title: Руководство по Интеграция Azure Active Directory с песочницей Salesforce | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6794d7eaccb488bb345227161f0bca02f14bc518
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57852582"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Руководство по Интеграция Azure Active Directory с песочницей Salesforce

В этом руководстве описано, как интегрировать Salesforce Sandbox с Azure Active Directory (Azure AD).

Песочницы позволяет создать несколько копий организации в отдельных средах для различных целей, например для разработки, тестирования и обучения, не подвергая риску данные и приложения в рабочей организации Salesforce.
Дополнительные сведения см. в статье  [Sandbox Types and Templates](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5) (Типы и шаблоны песочниц).

Интеграция Azure AD с Salesforce Sandbox обеспечивает следующие преимущества:

- С помощью Azure AD можно управлять доступом к Salesforce Sandbox.
- Вы можете включить автоматический вход пользователей в Salesforce Sandbox (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Технические условия

Чтобы настроить интеграцию Azure AD с Salesforce Sandbox, вам потребуется:

- подписка Azure AD;
- подписка Salesforce Sandbox с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Salesforce Sandbox из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Добавление Salesforce Sandbox из коллекции

Чтобы настроить интеграцию Salesforce Sandbox с Azure AD, нужно добавить Salesforce Sandbox из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Salesforce Sandbox из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"][3]

4. В поле поиска введите **Salesforce Sandbox**, выберите **Salesforce Sandbox** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Salesforce Sandbox в списке результатов](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе объясняется, как настроить и проверить единый вход Azure AD в Salesforce Sandbox с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Salesforce Sandbox соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Salesforce Sandbox.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Salesforce Sandbox.

Чтобы настроить и проверить единый вход Azure AD в Salesforce Sandbox, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Salesforce Sandbox](#create-a-salesforce-sandbox-test-user)** требуется для создания в Salesforce Sandbox пользователя Britta Simon, связанного с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Salesforce Sandbox.

**Чтобы настроить единый вход Azure AD в Salesforce Sandbox, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Salesforce Sandbox** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В верхней части экрана щелкните **Изменить режим единого входа**, чтобы выбрать режим **SAML**.

      ![Ссылка "Настройка единого входа"](./media/salesforce-sandbox-tutorial/tutorial_general_300.png)

3. В диалоговом окне **Выбрать метод единого входа** щелкните **Выбрать** для режима **SAML**, чтобы включить единый вход.

    ![Ссылка "Настройка единого входа"](./media/salesforce-sandbox-tutorial/tutorial_general_301.png)

4. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.
   
    ![Ссылка "Настройка единого входа"](./media/salesforce-sandbox-tutorial/tutorial_general_302.png)

5. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** выполните следующие действия.

    a. Щелкните **Отправить файл метаданных**.

    ![Передача файла метаданных](./media/salesforce-sandbox-tutorial/upload_metadata.png)

    2. Щелкните **значок папки**, выберите файл метаданных и нажмите кнопку **Отправить**.

    ![Выбор файла метаданных](./media/salesforce-sandbox-tutorial/browse_upload_metadata.png)

    > [!NOTE]
    > Вы получите доступ к файлу метаданных поставщика служб на портале администрирования Salesforce Sandbox, как описано далее в этом руководстве.

    c. После успешной передачи файла метаданных значение **URL-адрес ответа** будет автоматически добавлено в поле **URL-адрес ответа**.

    ![Сведения о домене и URL-адресах единого входа для приложения Salesforce Sandbox](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url1.png)

6. В разделе **Сертификат подписи SAML** нажмите **Загрузить**, чтобы загрузить **XML метаданных федерации**, а затем сохраните XML-файл на своем компьютере.

    ![Ссылка для скачивания сертификата](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

7. Откройте новую вкладку в браузере и войдите в учетную запись администратора Salesforce Sandbox.

8. Щелкните **Setup** (Настройка) под **значком параметров** в правом верхнем углу страницы.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure1.png)

9. В области навигации слева прокрутите вниз до элемента **Параметры** и щелкните **Удостоверение**, чтобы развернуть соответствующий раздел. Затем щелкните **Параметры единого входа**.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. На странице **Single Sign-On Settings** (Параметры единого входа) нажмите кнопку **Edit** (Изменить).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure3.png)

11. Выберите **SAML Enabled** (SAML включен), а затем щелкните **Save** (Сохранить).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

12. Чтобы настроить параметры единого входа SAML, щелкните **New from Metadata File** (Создать из файла метаданных).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

13. Щелкните **Выбрать файл** для отправки XML-файла метаданных, загруженного с портала Azure, и щелкните **Создать**.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/xmlchoose.png)

14. На странице **SAML Single Sign-On Settings** (Параметры единого входа SAML) поля заполняются автоматически. Щелкните "Сохранить".

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/salesforcexml.png)

15. На странице **Параметры единого входа** нажмите кнопку **Download Metadata** (Скачать метаданные), чтобы скачать файл метаданных поставщика служб. Используйте этот файл в разделе **Базовая конфигурация SAML** на портале Azure для настройки необходимых URL-адресов, как описано выше.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure4.png)

16. Если вы хотите настроить приложение в режиме, инициированном **SP**, для этого есть следующие требования:

    a. Вам необходимо получить проверенный домен.

    2. Необходимо настроить и включить домен в Salesforce Sandbox. Дальнейшие действия объясняются далее в этом руководстве.

    c. На портале Azure в разделе **Базовая конфигурация SAML** щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.
  
    ![Сведения о домене и URL-адресах единого входа для приложения Salesforce Sandbox](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    В текстовом поле **URL-адрес для входа** введите значение в следующем формате: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`.

    > [!NOTE]
    > Это значение следует скопировать с портала Salesforce Sandbox сразу после включения домена.

17. В разделе **Сертификат подписи SAML** щелкните **XML метаданных федерации** и сохраните XML-файл на компьютере.

    ![Ссылка для скачивания сертификата](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

18. Откройте новую вкладку в браузере и войдите в учетную запись администратора Salesforce Sandbox.

19. Щелкните **Setup** (Настройка) под **значком параметров** в правом верхнем углу страницы.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure1.png)

20. В области навигации слева прокрутите вниз до элемента **Параметры** и щелкните **Удостоверение**, чтобы развернуть соответствующий раздел. Затем щелкните **Параметры единого входа**.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

21. На странице **Single Sign-On Settings** (Параметры единого входа) нажмите кнопку **Edit** (Изменить).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure3.png)

22. Выберите **SAML Enabled** (SAML включен), а затем щелкните **Save** (Сохранить).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

23. Чтобы настроить параметры единого входа SAML, щелкните **New from Metadata File** (Создать из файла метаданных).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

24. Щелкните **Выбрать файл**, чтобы передать XML-файл метаданных, затем нажмите кнопку **Создать**.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/xmlchoose.png)

25. На странице **SAML Single Sign-On Settings** (Параметры единого входа SAML) поля заполняются автоматически. Введите имя конфигурации (например, *SPSSOWAAD_Test*) в текстовом поле **Имя** и нажмите кнопку "Сохранить".

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

26. Чтобы включить домен в приложении Salesforce Sandbox, выполните следующие действия:

    > [!NOTE]
    > Перед включением домена необходимо его создать в приложении Salesforce Sandbox. Дополнительные сведения см. в разделе [Define Your Domain Name](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US) (Определение доменного имени). Создав домен, убедитесь, что он настроен правильно.

    * В области навигации слева в Salesforce Sandbox щелкните **Company Settings** (Параметры компании), чтобы развернуть соответствующий раздел, и выберите пункт **My Domain** (Мой домен).

         ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

    * В разделе **Authentication Configuration** (Конфигурация проверки подлинности) щелкните **Edit** (Редактировать).

        ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

    * В разделе **Authentication Configuration** (Конфигурация проверки подлинности) в качестве **службы проверки подлинности** выберите имя параметра единого входа SAML, установленного при конфигурации единого входа в Salesforce Sandbox, после чего щелкните **Сохранить**.

        ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Создание пользователя Azure AD][100]

2. В верхней части экрана выберите **Новый пользователь**.

    ![Создание тестового пользователя Azure AD](./media/salesforce-sandbox-tutorial/create_aaduser_01.png) 

3. В разделе свойств пользователя сделайте следующее.

    ![Создание тестового пользователя Azure AD](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

    a. В поле **Имя** введите **BrittaSimon**.
  
    2. В **имя пользователя** тип поля **brittasimon\@yourcompanydomain.extension**  
    Например BrittaSimon@contoso.com.

    c. Выберите **Свойства**, установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="create-a-salesforce-sandbox-test-user"></a>Создание тестового пользователя Salesforce Sandbox

В этом разделе вы создадите в Salesforce Sandbox пользователя с именем Britta Simon. Salesforce Sandbox поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь в Salesforce Sandbox еще не существует, он создается при попытке доступа к Salesforce Sandbox. Salesforce также поддерживает автоматическую подготовку пользователей. Дополнительные сведения о настройке автоматической подготовки пользователей можно найти [здесь](salesforce-sandbox-provisioning-tutorial.md).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Salesforce Sandbox.

![Назначение роли пользователя][200]

**Чтобы назначить пользователя Britta Simon в Salesforce Sandbox, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Salesforce Sandbox**.

    ![Ссылка на Salesforce Sandbox в списке приложений](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Выберите **Добавить пользователя**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Salesforce Sandbox на панели доступа, вы автоматически войдете в приложение Salesforce Sandbox.
См. дополнительные сведения о [панели доступа](../user-help/active-directory-saas-access-panel-introduction.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Руководство по настройке Google Apps для автоматической подготовки пользователей](salesforce-sandbox-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-sandbox-tutorial/tutorial_general_203.png
