---
title: Руководство. Интеграция единого входа Azure Active Directory с ContractSafe Saml2 SSO | Документация Майкрософт
description: Сведения о настройке единого входа между Azure Active Directory и ContractSafe Saml2 SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9d8c9eba-6a90-4c8f-b387-a6ead4af00af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1509ae5262378b031eae47cf2cd07ec01448ef7d
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75553104"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-contractsafe-saml2-sso"></a>Руководство. Интеграция единого входа Azure Active Directory с ContractSafe Saml2 SSO

В этом руководстве описано, как интегрировать ContractSafe Saml2 SSO с Azure Active Directory (AAD). Интеграция ContractSafe Saml2 SSO с Azure AD обеспечивает перечисленные ниже возможности.

* С помощью AAD вы можете контролировать доступ к ContractSafe Saml2 SSO.
* Вы можете включить автоматический вход пользователей в ContractSafe Saml2 SSO с учетными записями AAD.
* Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>предварительные требования

Чтобы приступить к работе, потребуется следующее:

* подписка Azure AD; Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).
* подписка ContractSafe Saml2 SSO с поддержкой единого входа.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* ContractSafe Saml2 SSO поддерживает единый вход, инициированный **поставщиком удостоверений**.

## <a name="adding-contractsafe-saml2-sso-from-the-gallery"></a>Добавление ContractSafe Saml2 SSO из коллекции

Чтобы настроить интеграцию ContractSafe Saml2 SSO с Azure AD, необходимо добавить ContractSafe Saml2 SSO из коллекции в список управляемых приложений SaaS.

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавление из коллекции** в поле поиска введите **ContractSafe Saml2 SSO**.
1. Выберите **ContractSafe Saml2 SSO** в области результатов и добавьте это приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-single-sign-on-for-contractsafe-saml2-sso"></a>Настройка и проверка единого входа Azure AD для ContractSafe Saml2 SSO

Настройте и проверьте единый вход Azure AD в ContractSafe Saml2 SSO с помощью тестового пользователя **B.Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем Azure AD и соответствующим пользователем в ContractSafe Saml2 SSO.

Чтобы настроить и проверить единый вход Azure AD в ContractSafe Saml2 SSO, выполните действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.
    * **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с помощью пользователя B.Simon.
    * **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить пользователю B.Simon использовать единый вход Azure AD.
1. **[Настройка единого входа в ContractSafe Saml2 SSO](#configure-contractsafe-saml2-sso)** необходима, чтобы настроить параметры единого входа на стороне приложения.
    * **[Создание тестового пользователя ContractSafe Saml2 SSO](#create-contractsafe-saml2-sso-test-user)** требуется для того, чтобы в ContractSafe Saml2 SSO существовал пользователь B.Simon, связанный с одноименным пользователем в AAD.
1. **[Проверка единого входа](#test-sso)** необходима, чтобы убедиться в корректной работе конфигурации.

## <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **ContractSafe Saml2 SSO** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Правка базовой конфигурации SAML](common/edit-urls.png)

1. На странице **Настройка единого входа с помощью SAML** введите значения для следующих полей:

    а. В текстовом поле **Идентификатор** введите URL-адрес в формате `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`.

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в формате `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`.

    > [!NOTE]
    > Эти значения приведены для примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки ContractSafe Saml2 SSO](mailto:donne@contractsafe.com). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. Приложение ContractSafe Saml2 SSO ожидает проверочные утверждения SAML в определенном формате, и для этого нужно добавить сопоставления настраиваемых атрибутов в конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию.

    ![image](common/default-attributes.png)

1. В дополнение к описанному выше приложение ContractSafe Saml2 SSO ожидает несколько дополнительных атрибутов в ответе SAML, как показано ниже. Эти атрибуты также заранее заполнены, но вы можете изменить их в соответствии со своими требованиями.

    | Имя | Исходный атрибут|
    | ---------------| --------------- |
    | emailname | user.userprincipalname |
    | email | user.onpremisesuserprincipalname |

1. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** найдите элемент **XML метаданных федерации** и выберите **Скачать**, чтобы скачать сертификат и сохранить его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

1. Скопируйте требуемый URL-адрес в разделе **Настройка ContractSafe Saml2 SSO**.

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

В этом разделе описано, как включить единый вход в Azure для пользователя B.Simon, предоставив этому пользователю доступ к ContractSafe Saml2 SSO.

1. На портале Azure выберите **Корпоративные приложения**, а затем —**Все приложения**.
1. В списке приложений выберите **ContractSafe Saml2 SSO**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.

   ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Добавить пользователя"](common/add-assign-user.png)

1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор роли** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

## <a name="configure-contractsafe-saml2-sso"></a>Настройка единого входа на стороне ContractSafe Saml2 SSO

Чтобы настроить единый вход на стороне **ContractSafe Saml2 SSO**, нужно отправить скачанный файл **XML метаданных федерации** и соответствующие URL-адреса, скопированные на портале Azure, в [группу поддержки ContractSafe Saml2 SSO](mailto:donne@contractsafe.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-contractsafe-saml2-sso-test-user"></a>Создание тестового пользователя ContractSafe Saml2 SSO

В этом разделе описано, как создать пользователя B.Simon в приложении ContractSafe Saml2 SSO. Чтобы добавить пользователей на платформу ContractSafe Saml2 SSO, обратитесь в  [службу поддержки ContractSafe Saml2 SSO](mailto:donne@contractsafe.com). Перед использованием единого входа необходимо создать и активировать пользователей.

## <a name="test-sso"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку ContractSafe Saml2 SSO на панели доступа, вы автоматически войдете в приложение ContractSafe Saml2 SSO, для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Руководства по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Проверьте работу ContractSafe Saml2 SSO с AAD](https://aad.portal.azure.com/).