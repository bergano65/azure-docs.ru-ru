---
title: Руководство по Интеграция Azure Active Directory с Costpoint | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Costpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01168540e27605db0d240c0774159a710b5d5254
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71840105"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Руководство по Интеграция Costpoint с Azure Active Directory

В этом руководстве описано, как интегрировать Costpoint с Azure Active Directory (Azure AD). Интеграция Costpoint с Azure AD обеспечивает следующие возможности.

* С помощью Azure AD вы можете контролировать доступ к Costpoint.
* Вы можете включить автоматический вход пользователей в Costpoint с помощью учетных записей Azure AD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* подписка Costpoint с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход AAD в тестовой среде. Costpoint поддерживает единый вход, инициируемый **поставщиком услуг и поставщиком удостоверений**.

## <a name="generate-costpoint-metadata"></a>Создание метаданных Costpoint

Конфигурация единого входа SAML для Costpoint описана в руководстве **DeltekCostpoint711Security.pdf**. Скачайте это пошаговое руководство с сайта поддержки Deltek Costpoint и просмотрите раздел **SAML Single Sign-on Setup** (Настройка единого входа SAML)  > **Configure SAML Single Sign-on between Costpoint and Microsoft Azure** (Настройка единого входа SAML между Costpoint и Microsoft Azure). Согласно инструкциям создайте **файл XML метаданных федерации поставщика услуг с Costpoint**. 

![Служебная программа для настройки Costpoint](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Добавление Costpoint из коллекции

Чтобы интегрировать Costpoint с Azure AD, сначала добавьте Costpoint в список управляемых приложений SaaS из коллекции на портале Azure:

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.

1. В области навигации слева выберите службу **Azure Active Directory**.

   ![Кнопка Azure Active Directory](common/select-azuread.png)

1. Выберите **Корпоративные приложения** > **Все приложения**.

   ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

1. Чтобы добавить новое приложение, выберите **Новое приложение**.

   ![Кнопка "Создать приложение"](common/add-new-app.png)

1. В разделе **Добавление из коллекции** в поле поиска введите **Costpoint**.

   ![Costpoint в списке результатов](common/search-new-app.png)

1. Выберите **Costpoint** в списке результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>Настройка и проверка единого входа в Azure AD

Настройте и проверьте единый вход Azure AD в Costpoint с помощью тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Costpoint.

Чтобы настроить и проверить единый вход Azure AD в Costpoint, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Настройка Costpoint](#configure-costpoint)** необходима, чтобы настроить параметры единого входа SAML на стороне приложения.
1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя B.Simon.
1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить B.Simon использовать единый вход Azure AD.
1. **[Создание тестового пользователя Costpoint](#create-a-costpoint-test-user)** требуется для того, чтобы в Costpoint существовал пользователь B.Simon, связанный с одноименным пользователем в Azure AD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure:

1. На странице интеграции приложения **Costpoint** выберите **Единый вход**.

   ![Ссылка "Настройка единого входа"](common/select-sso.png)

1. Если у вас есть **файл метаданных** поставщика служб, выполните следующие действия в разделе *Базовая конфигурация SAML*:

   > [!NOTE]
   > Вы получаете файл метаданных поставщика службы в разделе [Создание метаданных Costpoint](#generate-costpoint-metadata). Сведения о том, как использовать файл, описаны далее в этом учебнике.
 
   1. Нажмите кнопку **Отправить файл метаданных**, выберите файл **XML метаданных федерации поставщика услуг**, ранее созданный Costpoint, а затем нажмите кнопку **Добавить**, чтобы отправить файл.

      ![Передача файла метаданных](./media/costpoint-tutorial/upload-metadata.png)
    
   1. После успешной передачи файла метаданных в разделе Costpoint автоматически заполняются значения **Идентификатор** и **URL-адрес ответа**.

      > [!NOTE]
      > Если значения **Идентификатор** и **URL-адрес ответа** автоматически не заполняются, введите эти значения вручную в соответствии с требованиями. Убедитесь, что правильно заданы значения **Идентификатор (сущности)** и **URL-адрес ответа (URL-адрес службы обработчика утверждений)** , а **URL-адрес ACS** содержит допустимый URL-адрес Costpoint, который заканчивается строкой **/LoginServlet.cps**.

   1. Щелкните **Задать дополнительные URL-адреса**. Для **состояния ретранслятора** введите значение в следующем формате:`system=[your system]` (например, **system=DELTEKCP**).

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните значок **копирования**, чтобы скопировать **URL-адрес метаданных федерации приложений** и сохранить его в Блокноте.

   ![Сертификат для подписи токена SAML](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>Настройка Costpoint

1. Вернитесь в программу для настройки Costpoint. В текстовом поле **IdP Federation Metadata XML** (XML метаданных федерации поставщика удостоверений) вставьте содержимое файла *URL-адреса метаданных федерации приложения*. 

   ![Служебная программа для настройки Costpoint](./media/costpoint-tutorial/config-utility-idp.png)

1. Следуйте инструкциям из руководства **DeltekCostpoint711Security.pdf**, чтобы завершить установку SAML для Costpoint.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем B.Simon.

1. На портале Azure на панели слева выберите **Azure Active Directory** > **Пользователи** > **Все пользователи**.

   ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

1. Выберите **Новый пользователь**.

   ![Кнопка "Новый пользователь"](common/new-user.png)

1. В свойствах **пользователя** выполните следующие действия:

   ![Диалоговое окно "Пользователь"](common/user-properties.png)

   1. В поле **Имя** введите **B.Simon**.
   
   1. В поле **Имя пользователя** введите `b.simon\@yourcompanydomain.extension` (например, B.Simon@contoso.com).
   
   1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.
   
   1. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход в Azure для пользователя B.Simon, предоставив этому пользователю доступ к Costpoint.

1. На портале Azure выберите **Корпоративные приложения > ** **Все приложения**.

1. Из списка приложений выберите **Costpoint**.

1. В разделе **Управление** на странице сводных данных о приложении выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**. В диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

   ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** в списке **Пользователи** выберите **B.Simon**. Затем щелкните **Выбрать**.

1. Если вы хотите получить значение роли в утверждении SAML, выберите в диалоговом окне **Выбор роли** подходящую роль для пользователя в предложенном списке, а затем щелкните **Выбрать**.

1. В диалоговом окне **Добавление назначения** выберите **Назначить**.

### <a name="create-a-costpoint-test-user"></a>Создание тестового пользователя Costpoint

В этом разделе описано, как создать пользователя в приложении Costpoint. Предположим, что **идентификатор пользователя** имеет значение **B.SIMON**, а имя пользователя — B.Simon Обратитесь к [группе поддержки клиентов Costpoint](https://www.deltek.com/about/contact-us), чтобы добавить пользователей на платформу Costpoint. Перед использованием единого входа необходимо создать и активировать пользователя.

После создания пользователя **метод проверки подлинности** для пользователя должен иметь значение **Active Directory**, должен быть установлен флажок **Единый вход SAML**, а имя пользователя из Azure Active Directory должно иметь значение **Идентификатор Active Directory или сертификата** (как показано на следующем снимке экрана).

![Пользователь Costpoint](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>Проверка единого входа

Щелкнув плитку Costpoint на панели доступа, вы автоматически войдете в приложение Costpoint, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список руководств по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
