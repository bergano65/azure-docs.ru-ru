---
title: Руководство по интеграции Azure Active Directory с Keeper Password Manager & Digital Vault | Документы Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Keeper Password Manager & Digital Vault.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: jeedes
ms.openlocfilehash: 88f84fba43959ee5e5b8d93446e4985a75697813
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685874"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Руководство по интеграции Azure Active Directory с Keeper Password Manager & Digital Vault

В этом учебнике описано, как интегрировать Keeper Password Manager & Digital Vault с Azure Active Directory (Azure AD).
Интеграция Keeper Password Manager & Digital Vault с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Keeper Password Manager & Digital Vault.
* Вы можете включить автоматический вход пользователей в Keeper Password Manager & Digital Vault (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.


## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Keeper Password Manager & Digital Vault, вам потребуется:

* подписка Azure AD; (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка на Keeper Password Manager & Digital Vault с поддержкой единого входа

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Keeper Password Manager & Digital Vault поддерживает единый вход инициированного **пакета обновления**.

* Keeper Password Manager & Digital Vault поддерживает **JIT-** подготовку пользователей.

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Добавление Keeper Password Manager & Digital Vault из коллекции

Чтобы настроить интеграцию Keeper Password Manager & Digital Vault с Azure AD, необходимо добавить Keeper Password Manager & Digital Vault из коллекции в список управляемых приложений SaaS.

1. Войдите на портал Azure с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. В области навигации слева выберите службу **Azure Active Directory**.
1. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.
1. Чтобы добавить новое приложение, выберите **Новое приложение**.
1. В разделе **Добавить из коллекции** в поле поиска введите **Keeper Password Manager & Digital Vault**.
1. На панели результатов выберите **Keeper Password Manager & Digital Vault**, а затем добавьте приложение. Подождите несколько секунд, пока приложение не будет добавлено в ваш клиент.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Настройка и проверка единого входа Azure AD для Keeper Password Manager & Digital Vault

Настройте и проверьте единый вход Azure AD в Keeper Password Manager & Digital Vault с помощью тестового пользователя **B. Simon**. Для обеспечения работы единого входа необходимо установить связь между пользователем AAD и соответствующим пользователем в Keeper Password Manager & Digital Vault.

Чтобы настроить и проверить единый вход Azure AD в Keeper Password Manager & Digital Vault, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-sso)** необходима, чтобы пользователи могли использовать эту функцию.

    * **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
    * **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.

1. **[Настройка единого входа в Keeper Password Manager & Digital Vault](#configure-keeper-password-manager--digital-vault-sso)** необходима, чтобы задать параметры единого входа на стороне приложения.
    * **[Создание тестового пользователя Keeper Password Manager & Digital Vault](#create-keeper-password-manager--digital-vault-test-user)** требуется для создания в Keeper Password Manager & Digital Vault пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
1. **[Проверка единого входа](#test-sso)** позволяет убедиться в правильности конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

Выполните следующие действия, чтобы включить единый вход Azure AD на портале Azure.

1. На портале Azure на странице интеграции с приложением **Keeper Password Manager & Digital Vault** найдите раздел **Управление** и выберите **Единый вход**.
1. На странице **Выбрать метод единого входа** выберите **SAML**.
1. На странице **Настройка единого входа с помощью SAML** щелкните значок "Изменить" (значок пера), чтобы открыть диалоговое окно **Базовая конфигурация SAML** и изменить параметры.

   ![Изменение базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** выполните приведенные ниже действия.

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: .
    * Для **единого входа в облако**: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Для **локального единого входа**: `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате:
    * Для **единого входа в облако**: `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * Для **локального единого входа**: `https://<KEEPER_FQDN>/sso-connect`

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: .
    * Для **единого входа в облако**: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Для **локального единого входа**: `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > Эти значения приведены для примера. Вместо них необходимо указать фактические значения URL-адреса входа, идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Keeper Password Manager & Digital Vault](https://keepersecurity.com/contact.html). Можно также посмотреть шаблоны в разделе **Базовая конфигурация SAML** на портале Azure.

1. Приложение Keeper Password Manager & Digital Vault ожидает утверждения SAML в определенном формате, который предполагает, что вы должны добавить сопоставления настраиваемых атрибутов в конфигурацию атрибутов токена SAML. На следующем снимке экрана показан список атрибутов по умолчанию.

    ![Изображение](common/default-attributes.png)

1. Кроме того, приложение Keeper Password Manager & Digital Vault ожидает несколько дополнительных атрибутов в ответе SAML, как показано ниже. Эти атрибуты также заранее заполнены, но вы можете изменить их в соответствии со своими требованиями.

    | Имя | Исходный атрибут|
    | ------------| --------- |
    | First | user.givenname |
    | Последний | user.surname |
    | Email | user.mail |

5. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

6. Требуемый URL-адрес можно скопировать из раздела **Настройка Keeper Password Manager & Digital Vault**.

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

В этом разделе описано, как позволить пользователю B. Simon использовать единый вход Azure, предоставив ему доступ к Keeper Password Manager & Digital Vault.

1. На портале Azure выберите **Корпоративные приложения**, а затем — **Все приложения**.
1. В списке приложений выберите **Keeper Password Manager & Digital Vault**.
1. На странице "Обзор" приложения найдите раздел **Управление** и выберите **Пользователи и группы**.
1. Выберите **Добавить пользователя**, а в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.
1. В диалоговом окне **Пользователи и группы** выберите **B.Simon** в списке пользователей, а затем в нижней части экрана нажмите кнопку **Выбрать**.
1. Если пользователям необходимо назначить роль, вы можете выбрать ее из раскрывающегося списка **Выберите роль**. Если для этого приложения не настроена ни одна роль, будет выбрана роль "Доступ по умолчанию".
1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Настройка единого входа в Keeper Password Manager & Digital Vault

Чтобы настроить единый вход на стороне **Keeper Password Manager & Digital Vault**, выполните инструкции в [Руководстве по поддержке Keeper](https://docs.keeper.io/sso-connect-guide/).

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>Создание тестового пользователя Keeper Password Manager & Digital Vault

Чтобы пользователи Azure AD могли выполнять вход в Keeper Password Manager & Digital Vault, они должны быть подготовлены в Keeper Password Manager & Digital Vault. Приложение поддерживает JIT-подготовку пользователей, поэтому после аутентификации пользователи будут созданы в приложении автоматически. Если вы хотите настроить пользователей вручную, обратитесь в [службу поддержки Keeper](https://keepersecurity.com/contact.html).

## <a name="test-sso"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью указанных ниже способов. 

* Выберите **Тестировать приложение** на портале Azure. Вы будете перенаправлены на URL-адрес входа в Keeper Password Manager & Digital Vault, где можно инициировать процесс входа. 

* Напрямую откройте URL-адрес входа в Keeper Password Manager & Digital Vault и инициируйте процесс входа.

* Вы можете использовать Панель доступа (Майкрософт). При нажатии плитки Keeper Password Manager & Digital Vault на панели доступа должна появиться страница входа в приложение Keeper Password Manager & Digital Vault. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Дальнейшие действия

После настройки Keeper Password Manager & Digital Vault вы можете применить управление сеансами, которое в реальном времени защищает конфиденциальные данные вашей организации от кражи и несанкционированного доступа. Управление сеансом является расширением функции условного доступа. [Узнайте, как применять управление сеансами с помощью Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).