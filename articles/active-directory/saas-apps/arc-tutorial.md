---
title: Руководство по интеграции единого входа Azure Active Directory с Arc Publishing — SSO | Документация Майкрософт
description: Сведения о том, как настроить единый вход Azure Active Directory в Arc Publishing — SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ae609583-f875-4cb8-b68e-1b0b7938e9a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19acb111cc672b0e044142b3f8ccdc7fb7572559
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893286"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-arc-publishing---sso"></a>Руководство по интеграции единого входа Azure Active Directory с Arc Publishing — SSO

В этом учебнике описано, как интегрировать Arc Publishing — SSO с Azure Active Directory (Azure AD). Интеграция Arc Publishing — SSO с Azure AD обеспечивает перечисленные ниже возможности.

* С помощью Azure AD вы можете контролировать доступ к Arc Publishing — SSO.
* Вы можете включить автоматический вход пользователей в Arc Publishing — SSO с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка Arc Publishing — SSO с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.



* Arc Publishing — SSO поддерживает единый вход, инициированный **поставщиком услуг и поставщиком удостоверений**.
* Arc Publishing SSO поддерживает **JIT**-подготовку пользователей.


## <a name="adding-arc-publishing---sso-from-the-gallery"></a>Добавление Arc Publishing — SSO из коллекции

Чтобы настроить интеграцию Arc Publishing — SSO с Azure AD, нужно добавить Arc Publishing — SSO из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Arc Publishing — SSO**.
1. Выберите **Arc Publishing — SSO** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.


## <a name="configure-and-test-azure-ad-single-sign-on-for-arc-publishing---sso"></a>Настройка и проверка единого входа Azure AD для Arc Publishing — SSO

Настройте и проверьте единый вход Azure AD в Arc Publishing — SSO с помощью тестового пользователя **B. Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Arc Publishing — SSO.

Чтобы настроить и проверить единый вход Azure AD в Arc Publishing — SSO, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в Arc Publishing — SSO](#configure-arc-publishing---sso-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя Arc Publishing — SSO](#create-arc-publishing---sso-test-user)** требуется для того, чтобы в Arc Publishing — SSO существовал пользователь B. Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Arc Publishing — SSO** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, в разделе **Базовая конфигурация SAML** введите значения следующих полей.

    a. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://www.okta.com/saml2/service-provider/<Unique ID>`.

    б) В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`.

1. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`.

    > [!NOTE]
    > Эти значения приведены для примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Arc Publishing — SSO](mailto:inf@washpost.com). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. Приложение Arc Publishing — SSO ожидает проверочные утверждения SAML в определенном формате, который требует добавить сопоставления настраиваемых атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию.

    ![image](common/edit-attribute.png)

1. В дополнение к описанному выше приложение Arc Publishing — SSO ожидает несколько дополнительных атрибутов в ответе SAML, как показано ниже. Эти атрибуты также заранее заполнены, но вы можете изменить их в соответствии со своими требованиями.


    | ИМЯ | Исходный атрибут|
    | ---------------| --------------- |    
    | firstName | user.givenname |
    | lastName | user.surname |
    | email | user.mail |
    | groups | user.assignedroles |

    > [!NOTE]
    > Здесь атрибут **groups** сопоставляется с **user.assignedroles**. Это пользовательские роли, созданные в Azure AD для сопоставления с именами групп в приложении. [Здесь](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) приведены дополнительные рекомендации о создании настраиваемых ролей в Azure AD.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите пункт **Сертификат (Base64)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. Скопируйте требуемый URL-адрес в разделе **Настройка Arc Publishing — SSO**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе описано, как на портале Azure создать тестового пользователя с именем B.Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.
1. В верхней части экрана выберите **Новый пользователь**.
1. В разделе **Свойства пользователя** выполните следующие действия.
   1. В поле **Имя** введите `B.Simon`.  
   1. В поле **Имя пользователя** введите username@companydomain.extension. Например, `B.Simon@contoso.com`.
   1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.
   1. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю B. Simon доступ к Arc Publishing — SSO, чтобы он мог использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **Arc Publishing — SSO**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-arc-publishing---sso-sso"></a>Настройка единого входа в Arc Publishing — SSO

Чтобы настроить единый вход на стороне **Arc Publishing — SSO**, нужно отправить скачанный **сертификат (Base64)** и соответствующие URL-адреса, скопированные на портале Azure, [группе поддержки Arc Publishing — SSO](mailto:inf@washpost.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-arc-publishing---sso-test-user"></a>Создание тестового пользователя в Arc Publishing — SSO

В этом разделе вы создадите в Arc Publishing — SSO пользователя с именем Britta Simon. Приложение Arc Publishing — SSO поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Arc Publishing — SSO, он создается после проверки подлинности.

> [!Note]
> Чтобы создать пользователя вручную, обратитесь к [группе поддержки Arc Publishing — SSO](mailto:inf@washpost.com).

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Arc Publishing — SSO на Панели доступа, вы автоматически войдете в приложение Arc Publishing — SSO, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте использовать Arc Publishing — SSO с Azure AD](https://aad.portal.azure.com/)

