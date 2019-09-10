---
title: Руководство по интеграции единого входа Azure Active Directory c Palo Alto Networks (GlobalProtect) | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Palo Alto Networks (GlobalProtect).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03bef6f2-3ea2-4eaa-a828-79c5f1346ce5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8faa1d0ecfec629487c9cddee6a01fb9f809393f
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165987"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-palo-alto-networks---globalprotect"></a>Руководство по интеграции единого входа Azure Active Directory c Palo Alto Networks (GlobalProtect)

В этом учебнике описано, как интегрировать Palo Alto Networks (GlobalProtect) с Azure Active Directory (Azure AD). Интеграция Palo Alto Networks (GlobalProtect) с Azure AD обеспечивает следующие возможности:

* Контроль доступа к Palo Alto Networks (GlobalProtect) с помощью Azure AD.
* Автоматический вход пользователей в Palo Alto Networks (GlobalProtect) с использованием учетной записи Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка Palo Alto Networks (GlobalProtect) с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Palo Alto Networks (GlobalProtect) поддерживает единый вход, инициированный **поставщиком услуг**.
* Palo Alto Networks (GlobalProtect) поддерживает **JIT**-подготовку пользователей.

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Добавление Palo Alto Networks (GlobalProtect) из коллекции

Чтобы настроить интеграцию Palo Alto Networks (GlobalProtect) с Azure AD, необходимо добавить Palo Alto Networks (GlobalProtect) из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** введите **Palo Alto Networks (GlobalProtect)** в поле поиска.
1. Выберите **Palo Alto Networks (GlobalProtect)** на панели результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-palo-alto-networks---globalprotect"></a>Настройка и проверка единого входа Azure AD для Palo Alto Networks (GlobalProtect)

Настройте и проверьте единый вход Azure AD в Palo Alto Networks (GlobalProtect) с помощью тестового пользователя **B. Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Palo Alto Networks (GlobalProtect).

Чтобы настроить и проверить единый вход Azure AD в Palo Alto Networks (GlobalProtect), вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в Palo Alto Networks (GlobalProtect)](#configure-palo-alto-networks---globalprotect-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя Palo Alto Networks (GlobalProtect)](#create-palo-alto-networks---globalprotect-test-user)** требуется, чтобы в Palo Alto Networks (GlobalProtect) существовал пользователь B. Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Palo Alto Networks (GlobalProtect)** найдите раздел **Управление** и щелкните **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Базовая конфигурация SAML** введите значения следующих полей.

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<Customer Firewall URL>`.

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате: `https://<Customer Firewall URL>/SAML20/SP`.

    > [!NOTE]
    > Эти значения приведены для примера. Необходимо обновить эти значения действующим URL-адресом для входа и идентификатором. Для получения этих значений обратитесь в [службу поддержки клиентов Palo Alto Networks (GlobalProtect)](https://support.paloaltonetworks.com/support). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите элемент **XML метаданных федерации** и выберите **Скачать**, чтобы скачать сертификат и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

1. Скопируйте требуемый URL-адрес из раздела **Настройка Palo Alto Networks (GlobalProtect)** .

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

В этом разделе описано, как предоставить пользователю B. Simon доступ к Palo Alto Networks (GlobalProtect), чтобы он мог использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. Выберите **Palo Alto Networks (GlobalProtect)** из списка приложений.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-palo-alto-networks---globalprotect-sso"></a>Настройка единого входа в Palo Alto Networks (GlobalProtect)

1. В другом окне браузера откройте пользовательский интерфейс администратора брандмауэров Palo Alto с правами администратора.

2. Щелкните **Device** (Устройство).

    ![Настройка единого входа в Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Чтобы импортировать файл метаданных, в левой области навигации выберите **SAML Identity Provider** (Поставщик удостоверений SAML) и нажмите кнопку Import (Импорт).

    ![Настройка единого входа в Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Сделайте следующее в окне Import (Импорт).

    ![Настройка единого входа в Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Укажите имя в текстовом поле **Profile Name** (Имя профиля), например Azure AD GlobalProtect.

    b. В разделе **Identity Provider Metadata** (Метаданные поставщика удостоверений) щелкните **Browse** (Обзор) и выберите файл metadata.xml, загруженный ранее с портала Azure.

    c. Щелкните **ОК**

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Создание тестового пользователя в Palo Alto Networks (GlobalProtect)

В этом разделе создается пользователь B. Simon в приложении Palo Alto Networks (GlobalProtect). Приложение Palo Alto Networks (GlobalProtect) поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Palo Alto Networks (GlobalProtect), он создается после проверки подлинности.

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Palo Alto Networks (GlobalProtect) на панели доступа, вы автоматически войдете в приложение Palo Alto Networks (GlobalProtect), для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Попробуйте использовать Palo Alto Networks (GlobalProtect) с Azure AD](https://aad.portal.azure.com/)