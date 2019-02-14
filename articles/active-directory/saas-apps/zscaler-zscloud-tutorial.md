---
title: Руководство. Интеграция Azure Active Directory с Zscaler ZSCloud | Документы Майкрософт
description: Вы можете узнать, как настроить единый вход Azure Active Directory в Zscaler ZSCloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 411d5684-a780-410a-9383-59f92cf569b5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c70d58669fe6351c67a54c282ee1310fb52ed50a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56218063"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Руководство. Интеграция Azure Active Directory с Zscaler ZSCloud

Это руководство описывает, как интегрировать Zscaler ZSCloud с Azure Active Directory (Azure AD).

Интеграция Azure AD с Zscaler ZSCloud обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Zscaler ZSCloud.
- Вы можете включить автоматический вход пользователей в Zscaler ZSCloud (единый вход) с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Zscaler ZSCloud, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа Zscaler ZSCloud.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Zscaler ZSCloud из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Добавление Zscaler ZSCloud из коллекции

Чтобы настроить интеграцию Zscaler ZSCloud с Azure AD, нужно добавить Zscaler ZSCloud из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Zscaler ZSCloud из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"][3]

4. В поле поиска введите **Zscaler ZSCloud**, выберите **Zscaler ZSCloud** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Zscaler ZSCloud в списке результатов](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

Этот раздел описывает настройку и проверку единого входа Azure AD в Zscaler ZSCloud с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD, нужно знать, какой пользователь в Zscaler ZSCloud соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в Zscaler ZSCloud.

Чтобы настроить и проверить единый вход Azure AD в Zscaler ZSCloud, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Zscaler ZSCloud](#creating-a-zscaler-zscloud-test-user)** требуется для создания в Zscaler ZSCloud пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

Этот раздел описывает, как включить единый вход Azure AD на портале Azure и настроить его в приложении Zscaler ZSCloud.

**Чтобы настроить единый вход Azure AD в Zscaler ZSCloud, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Zscaler ZSCloud** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Выбрать метод единого входа** щелкните **Выбрать** для режима **SAML**, чтобы включить единый вход.

    ![Настройка единого входа](common/tutorial_general_301.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Настройка единого входа](common/editconfigure.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    ![Сведения о домене и URL-адресах единого входа приложения Zscaler ZSCloud](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_url.png)

    В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение Zscaler ZSCloud.

    > [!NOTE] 
    > Вместо него нужно указать фактический URL-адрес для входа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Zscaler Two](https://help.zscaler.com/).

5. Приложение Zscaler ZSCloud ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **User Attributes & Claims** (Атрибуты пользователя и утверждения) на странице интеграции приложения. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **User Attributes & Claims** (Атрибуты пользователя и утверждения).

    ![Ссылка "Атрибуты"](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_attribute.png)

6. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.

    | ИМЯ  | Исходный атрибут  |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![изображение](./common/new_save_attribute.png)
    
    ![изображение](./common/new_attribute_details.png)

    б) В списке **Источник атрибута** выберите значение атрибута.

    c. Нажмите кнопку **ОК**.

    4.3. Выберите команду **Сохранить**.

    > [!NOTE]
    > Перейдите по [этой ссылке](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management), чтобы прочитать о настройке роли в Azure Active Directory.

7. На странице **сертификата подписи SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_certificate.png) 

8. Требуемый URL-адрес можно скопировать из раздела **Настройка Zscaler ZSCloud**.

    a. URL-адрес входа.

    b. Идентификатор Azure AD

    c. URL-адрес выхода.

    ![Конфигурация Zscaler ZSCloud](common/configuresection.png)

9. В другом окне браузера войдите на свой корпоративный сайт Zscaler ZSCloud в качестве администратора.

10. Выберите **Administration (Администрирование) > Authentication (Проверка подлинности) > Authentication Settings (Параметры проверки подлинности)** и выполните следующие действия:
   
    ![Администрирование](./media/zscaler-zscloud-tutorial/ic800206.png "Администрирование")

    a. В разделе Authentication Type (Тип проверки подлинности) выберите **SAML**.

    б) Нажмите кнопку **Configure SAML** (Настроить SAML).

11. В окне **Изменить параметры SAML** выполните следующие действия и нажмите кнопку "Сохранить".  
            
    ![Управление пользователями и проверкой подлинности](./media/zscaler-zscloud-tutorial/ic800208.png "Управление пользователями и проверкой подлинности")
    
    a. В текстовое поле **SAML Portal URL** (URL-адрес портала SAML) вставьте **URL-адрес входа**, скопированный на портале Azure.

    б) В текстовое поле **Login Name Attribute** (Атрибут имени входа) введите **NameID**.

    c. Нажмите **Upload** (Отправить), чтобы загрузить сертификат для подписи SAML, который вы скачали на портале Azure в разделе **Public SSL Certificate** (Публичный SSL-сертификат).

    4.3. Включите параметр **Enable SAML Auto-Provisioning** (Включить автоматическую подготовку SAML).

    д. В текстовое поле **User Display Name Attribute** (Атрибут отображаемого имени пользователя) введите **displayName**, если вы хотите включить автоматическую подготовку SAML для атрибутов displayName.

    Е. В текстовое поле **Group Name Attribute** (Атрибут имени группы) введите **memberOf**, если вы хотите включить автоматическую подготовку SAML для атрибутов memberOf.

    ж. В поле **Department Name Attribute** (Атрибут имени отдела) введите **department**, если вы хотите включить автоматическую подготовку SAML для атрибутов department.

    i. Выберите команду **Сохранить**.

12. На странице **Настройка проверки подлинности пользователей** выполните следующие действия.

    ![Администрирование](./media/zscaler-zscloud-tutorial/ic800207.png)

    a. Наведите указатель мыши на меню **Activation** (Активация) в нижнем левом углу.

    б) Щелкните **Активировать**.

## <a name="configuring-proxy-settings"></a>Настройка параметров прокси-сервера
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Настройка параметров прокси-сервера в Internet Explorer

1. Запустите **Internet Explorer**.

1. В меню **Сервис** выберите **Свойства браузера**, чтобы открыть диалоговое окно **Свойства браузера**.   
    
     ![Свойства браузера](./media/zscaler-zscloud-tutorial/ic769492.png "Свойства браузера")

1. Щелкните вкладку **Подключения** .   
  
     ![Подключения](./media/zscaler-zscloud-tutorial/ic769493.png "Подключения")

1. Нажмите кнопку **Настройка сети**, чтобы открыть диалоговое окно **Настройка сети**.

1. В разделе "Прокси-сервер" выполните следующие действия.   
   
    ![Прокси-сервер](./media/zscaler-zscloud-tutorial/ic769494.png "Прокси-сервер")

    a. Установите флажок **Использовать прокси-сервер для локальной сети**.

    б) В текстовом поле "Адрес" введите **gateway.Zscaler ZSCloud.net**.

    c. В текстовом поле "Порт" введите **80**.

    4.3. Установите флаг **Не использовать прокси-сервер для локальных адресов**.

    д. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Настройка параметров локальной сети**.

1. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Свойства браузера**.

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

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

### <a name="creating-a-zscaler-zscloud-test-user"></a>Создание тестового пользователя Zscaler ZSCloud

Цель этого раздела — создать пользователя с именем Britta Simon в Zscaler ZSCloud. Приложение Zscaler ZSCloud поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к Zscaler ZSCloud (если он еще не создан).
>[!Note]
>Если вам нужно создать пользователя вручную, обратитесь в  [службу поддержки Zscaler ZSCloud](https://help.zscaler.com/).

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

Этот раздел описывает, как позволить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Zscaler ZSCloud.

1. На портале Azure перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201]

2. В списке приложений выберите **Zscaler ZSCloud**.

    ![Настройка единого входа](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_app.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в нижней части экрана нажмите кнопку **Выбрать**.

    ![изображение](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_users.png)

6. В диалоговом окне **Выбор роли** выберите соответствующие роли пользователей из списка, а затем нажмите кнопку **Выбрать** в нижней части экрана.

    ![изображение](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_roles.png)

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

    ![изображение](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_assign.png)

### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "Zscaler ZSCloud" на панели доступа, вы автоматически войдете в приложение Zscaler ZSCloud.
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
