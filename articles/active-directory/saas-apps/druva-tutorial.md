---
title: Руководство по интеграции единого входа Azure Active Directory с Druva | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16b23ef246561d052935642c323c2d830e21cbe7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "73570197"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-druva"></a>Руководство по интеграции единого входа Azure Active Directory с Druva

В этом руководстве описано, как интегрировать Druva с Azure Active Directory (Azure AD). Интеграция Druva с Azure AD обеспечивает следующие возможности.

* Контроль того, кто имеет доступ к Druva, с помощью Azure AD.
* Включение автоматического входа пользователей в Druva с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка Druva с поддержкой единого входа (SSO).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Druva поддерживает единый вход, инициированный **поставщиком услуг и поставщиком удостоверений**

> [!NOTE]
> Идентификатор этого приложения — фиксированное строковое значение, поэтому в одном клиенте можно настроить только один экземпляр.

## <a name="adding-druva-from-the-gallery"></a>Добавление Druva из коллекции

Чтобы настроить интеграцию Druva с Azure AD, необходимо добавить Druva из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Druva**.
1. Выберите **Druva** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-druva"></a>Настройка и проверка единого входа Azure AD для Druva

Настройте и проверьте единый вход Azure AD в Druva с помощью тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Druva.

Чтобы настроить и проверить единый вход Azure AD в Druva, выполните действия их следующих стандартных блоков.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    * **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    * **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в Druva](#configure-druva-sso)** необходима чтобы настроить параметры единого входа на стороне приложения.
    * **[Создание тестового пользователя Druva](#create-druva-test-user)** требуется для того, чтобы в Druva существовал пользователь B.Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Druva** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

1. Если нужно настроить приложение в инициируемом режиме **выдающей точки распространения** в разделе **Базовые настройки SAML**, то пользователю для этого не нужно ничего делать, так как это приложение уже предварительно интегрировано в Azure.

1. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    В текстовом поле **URL-адрес входа** введите URL-адрес: `https://login.druva.com/api/commonlogin/samlconsume`.

1. Выберите команду **Сохранить**.

1. Приложение Druva ожидает проверочные утверждения SAML в определенном формате, поэтому в вашу конфигурацию атрибутов токена SAML следует добавить сопоставления настраиваемых атрибутов. На следующем снимке экрана показан список атрибутов по умолчанию.

    ![image](common/default-attributes.png)

1. В дополнение к описанному выше приложение Druva ожидает несколько дополнительных атрибутов в ответе SAML, как показано ниже. Эти атрибуты также заранее заполнены, но вы можете изменить их в соответствии со своими требованиями.

    | ИМЯ | Исходный атрибут|
    | ------------------- | -------------------- |
    | emailAddress | user.email |
    | druva_auth_token | Токен единого входа, созданный из консоли администрирования DCP, без кавычек.  Например:  X-XXXXX-XXXX-S-A-M-P-L-E+TXOXKXEXNX=. Azure автоматически добавляет кавычки для токена проверки подлинности. |

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите пункт **Сертификат (Base64)** и щелкните **Скачать**, чтобы скачать сертификат. Сохраните этот сертификат на компьютере.

    ![Ссылка для скачивания сертификата](common/certificatebase64.png)

1. Скопируйте требуемые URL-адреса из раздела **Настройка Druva**.

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

В этом разделе описано, как включить единый вход в Azure для пользователя B.Simon, предоставив этому пользователю доступ к Druva.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **Druva**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-druva-sso"></a>Настройка единого входа Druva

1. В другом окне браузера войдите на свой корпоративный веб-сайт Druva с учетными данными администратора.

1. Щелкните логотип Druva в левом верхнем углу, а затем щелкните **Параметры облака Druva**.

    ![Параметры](./media/druva-tutorial/ic795091.png "Параметры")

1. На вкладке **Единый вход** щелкните **Изменить**.

    ![Параметры единого входа](./media/druva-tutorial/ic795092.png "Параметры единого входа")

1. На странице **Изменить параметры единого входа** сделайте следующее:

    ![Параметры единого входа](./media/druva-tutorial/ic795095.png "Параметры единого входа")

    1. В текстовое поле **ID Provider Login URL** (URL-адрес для входа поставщика удостоверений) вставьте **URL-адрес входа**, скопированный на портале Azure.

    1. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат поставщика удостоверений** .

       > [!NOTE]
       > Чтобы включить единый вход для администраторов, поставьте флажки на **Вход администраторов в облако Druva с помощью поставщика единого входа** и **Позволить отказоустойчивый доступ к облаку Druva администраторам (рекомендуется)** . Druva рекомендует включить **отказоустойчивость для администраторов**, чтобы они имели доступ к консоли DCP в случае любых сбоев в поставщике удостоверений. Он также позволяет администраторам использовать единый вход и пароль DCP для доступа к консоли DCP.

    1. Выберите команду **Сохранить**. Это обеспечивает доступ к облачной платформе Druva с помощью единого входа.

### <a name="create-druva-test-user"></a>Создание тестового пользователя в Druva

В этом разделе описано, как создать пользователя с именем B.Simon в приложении Druva. Приложение Druva поддерживает JIT-подготовку пользователей, которая включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Druva, он создается после проверки подлинности.

## <a name="test-sso"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Druva на Панели доступа, вы автоматически войдете в приложение Druva, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Пробное использование Druva с Azure AD](https://aad.portal.azure.com/)