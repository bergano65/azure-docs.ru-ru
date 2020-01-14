---
title: Руководство. Интеграция единого входа Azure Active Directory с Maxient Conduct Manager Software | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Maxient Conduct Manager Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85e71b76-cac3-4ce6-a35f-796d2cb7bdb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1a657a7d57b3e725b0ae92b5110935c0aecf73f
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75533037"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maxient-conduct-manager-software"></a>Руководство. Интеграция единого входа Azure Active Directory с Maxient Conduct Manager Software

В этом руководстве описано, как интегрировать Maxient Conduct Manager Software с Azure Active Directory (AAD). Интеграция Maxient Conduct Manager Software с Azure AD обеспечивает следующие возможности.

* С помощью Azure AD можно контролировать доступ к Maxient Conduct Manager Software.
* Автоматический вход пользователей в Maxient Conduct Manager Software с учетными записями AAD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD; Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* подписка Maxient Conduct Manager Software с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.



* Maxient Conduct Manager Software поддерживает единый вход, инициированный **поставщиком услуг и поставщиком удостоверений**.

> [!NOTE]
> Идентификатор этого приложения — фиксированное строковое значение, поэтому в одном клиенте можно настроить только один экземпляр.

## <a name="adding-maxient-conduct-manager-software-from-the-gallery"></a>Добавление Maxient Conduct Manager Software из коллекции

Чтобы настроить интеграцию Maxient Conduct Manager Software с Azure AD, вам потребуется добавить Maxient Conduct Manager Software из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Maxient Conduct Manager Software**.
1. Выберите **Maxient Conduct Manager Software** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.


## <a name="configure-and-test-azure-ad-single-sign-on-for-maxient-conduct-manager-software"></a>Настройка и проверка единого входа Azure AD для Maxient Conduct Manager Software

Настройте и проверьте единый вход Azure AD в Maxient Conduct Manager Software с помощью тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем AAD и соответствующим пользователем в Maxient Conduct Manager Software.

Чтобы настроить и проверить единый вход Azure AD в Maxient Conduct Manager Software, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в Maxient Conduct Manager Software](#configure-maxient-conduct-manager-software-sso)** требуется, чтобы определить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя Maxient Conduct Manager Software](#create-maxient-conduct-manager-software-test-user)** нужно для того, чтобы в Maxient Conduct Manager Software существовал пользователь B.Simon, связанный с одноименным пользователем в AAD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Maxient Conduct Manager Software** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. В разделе  **Базовая конфигурация SAML** приложение предварительно настроено в режиме, инициированном **поставщиком удостоверений** , и для приложения заданы требуемые URL-адреса в Azure. Пользователь должен сохранить конфигурацию, нажав кнопку **Сохранить** .

1. Чтобы настроить приложение для работы в режиме, инициируемом **поставщиком услуг**, щелкните **Задать дополнительные URL-адреса** и выполните следующие действия.

    В текстовом поле **URL-адрес входа** введите URL-адрес в формате `https://cm.maxient.com/<SCHOOLCODE>`.

    > [!NOTE]
    > Это значение приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь в [службу поддержки клиентов Maxient Conduct Manager Software](mailto:support@maxient.com). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложений** и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/copy-metadataurl.png)

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

В этом разделе описано, как включить единый вход Azure для пользователя B.Simon, предоставив этому пользователю доступ к Maxient Conduct Manager Software.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **Maxient Conduct Manager Software**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-maxient-conduct-manager-software-sso"></a>Настройка единого входа на стороне Maxient Conduct Manager Software

Чтобы настроить единый вход на стороне **Maxient Conduct Manager Software**, отправьте [группе поддержки Maxient Conduct Manager Software](mailto:support@maxient.com)**URL-адрес метаданных федерации приложений**. Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-maxient-conduct-manager-software-test-user"></a>Создание тестового пользователя в Maxient Conduct Manager Software

В этом разделе описано, как создать пользователя B.Simon в приложении Maxient Conduct Manager Software. Обратитесь к  [группе поддержки Maxient Conduct Manager Software](mailto:support@maxient.com), чтобы добавить пользователей в Maxient Conduct Manager Software. Перед использованием единого входа необходимо создать и активировать пользователей.

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "Maxient Conduct Manager Software" на Панели доступа вы автоматически войдете в приложение Maxient Conduct Manager Software, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Проверьте работу Maxient Conduct Manager Software с AAD](https://aad.portal.azure.com/)

