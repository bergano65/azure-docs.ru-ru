---
title: Руководство по Интеграции единого входа Azure Active Directory с Concur Travel and Expense | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Concur Travel and Expense.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6401ace4-71c0-4778-8b15-900f2f5f0c4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dddd9f6904aa5ef7840850792aeabf04666dddc
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373413"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Руководство по Интеграции единого входа Azure Active Directory с Concur Travel and Expense

В этом руководстве описано, как интегрировать Concur Travel and Expense с Azure Active Directory (Azure AD). Интеграция Concur Travel and Expense с Azure AD обеспечивает следующие возможности:

* Контроль доступа к Concur Travel and Expense с помощью Azure AD.
* Автоматический вход пользователей в Concur Travel and Expense с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* Подписка Concur Travel and Expense.
* Роль "Администратор компании" в учетной записи пользователя Concur. Вы можете проверить, есть ли у вас подходящий доступ, перейдя к [инструменту самообслуживания единого входа Concur](https://www.concursolutions.com/nui/authadmin/ssoadmin). Если у вас нет доступа, обратитесь в группу поддержки Concur или к руководителю проекта реализации. 

## <a name="scenario-description"></a>Описание сценария

В этом учебнике вы настроите и проверите единый вход Azure AD.

* Приложение Concur Travel and Expense поддерживает единый вход, инициированный **поставщиком удостоверений** и **поставщиком услуг**.
* Приложение Concur Travel and Expense поддерживает тестирование единого входа в рабочей среде и среде реализации. 

> [!NOTE]
> Идентификатор этого приложения является фиксированным строковым значением для каждого из трех регионов: США, EMEA (Европа, Ближний Восток и Африка), Китай. Поэтому для каждого региона в одном клиенте можно настроить только один экземпляр. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>Добавление Concur Travel and Expense из галереи

Чтобы настроить интеграцию Concur Travel and Expense с Azure AD, необходимо добавить Concur Travel and Expense из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **Concur Travel and Expense**.
1. Выберите **Concur Travel and Expense** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-concur-travel-and-expense"></a>Настройка и проверка единого входа Azure AD для Concur Travel and Expense

Настройте и проверьте единый вход Azure AD в Concur Travel and Expense с помощью тестового пользователя **B.Simon**. Чтобы обеспечить работу единого входа, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Concur Travel and Expense.

Чтобы настроить и проверить единый вход Azure AD в Concur Travel and Expense, выполните следующие стандартные блоки действий:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в Concur Travel and Expense](#configure-concur-travel-and-expense-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    1. **[Создание тестового пользователя Concur Travel and Expense](#create-concur-travel-and-expense-test-user)** требуется для того, чтобы в Concur существовал пользователь B.Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Concur Travel and Expense** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. В разделе **Базовая конфигурация SAML** приложение предварительно настроено в режиме инициированном **IDP** и ему заданы требуемые URL-адреса в Azure. Пользователь должен сохранить конфигурацию, нажав кнопку **Сохранить**.

    > [!NOTE]
    > Идентификатор (идентификатор сущности) и URL-адрес ответа (URL-адрес службы обработчика утверждений) относятся к отдельному региону. Выбирайте их в зависимости от центра обработки данных сущности Concur. Если вы не знаете центр обработки данных сущности Concur, обратитесь в группу поддержки Concur. 

5. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Атрибут пользователя** и изменить параметры. Уникальный идентификатор пользователя должен соответствовать login_ID пользователя Concur. Как правило, следует изменить **user.userprincipalname** на **user.mail**.

    ![Изменение атрибута пользователя](common/edit-attribute.png)

6. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите элемент **XML метаданных федерации** и нажмите кнопку **Скачать**, чтобы скачать XML-файл метаданных и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

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

В этом разделе описано, как включить единый вход в Azure для пользователя B.Simon, предоставив этому пользователю доступ к Concur Travel and Expense.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **Concur Travel and Expense**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-concur-travel-and-expense-sso"></a>Настройка единого входа Concur Travel and Expense

1. Чтобы настроить единый вход на стороне приложения **Concur Travel and Expense**, необходимо отправить скачанный **XML-файл метаданных федерации** в [инструмент самообслуживания для единого входа Concur](https://www.concursolutions.com/nui/authadmin/ssoadmin) и войти в систему с помощью учетной записи с ролью "Администратор компании". 

1. Щелкните **Добавить**.
1. Введите пользовательское имя для IdP, например "Azure AD (US)". 
1. Щелкните **Upload XML File** (Передать XML-файл) и присоедините **XML-файл метаданных федерации**, который вы скачали ранее.
1. Щелкните **Add Metadata** (Добавить метаданные), чтобы сохранить изменения.

    ![Снимок экрана инструмента самообслуживания единого входа Concur](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>Создание тестового пользователя Concur Travel and Expense

В этом разделе описано, как создать пользователя B.Simon в Concur Travel and Expense. Обратитесь в группу поддержки Concur Travel and Expense, чтобы добавить пользователей на платформу Concur Travel and Expense. Перед использованием единого входа необходимо создать и активировать пользователей. 

> [!NOTE]
> Идентификатор входа Concur для В. Simon должен соответствовать уникальному идентификатору B. Simon в Azure AD. Например, если в качестве уникального идентификатора B. Simon в Azure AD используется `B.Simon@contoso.com`, идентификатор входа Concur для B. Simon также должен быть `B.Simon@contoso.com`. 

## <a name="configure-concur-mobile-sso"></a>Настройка единого входа в Concur для мобильного устройства
Чтобы включить единый вход Concur для мобильного устройства, необходимо предоставить группе поддержки Concur **URL-адрес пользовательского доступа**. Чтобы получить **URL-адрес пользовательского доступа** из Azure AD, выполните следующие действия:
1. Перейдите к разделу **Корпоративные приложения**.
1. Щелкните **Concur Travel and Expense**.
1. Щелкните **Свойства**.
1. Скопируйте **URL-адрес пользовательского доступа** и предоставьте этот URL-адрес группе поддержки Concur.

> [!NOTE]
> Возможность самостоятельной настройки единого входа недоступна, поэтому обратитесь в группу поддержки Concur, чтобы включить единый вход для мобильного устройства. 

## <a name="test-sso"></a>Проверка единого входа 

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Concur Travel and Expense на панели доступа, вы автоматически войдете в приложение Concur Travel and Expense, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Пробное использование Concur Travel and Expense с Azure AD](https://aad.portal.azure.com/)

