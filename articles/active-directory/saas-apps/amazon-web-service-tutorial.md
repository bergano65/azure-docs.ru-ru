---
title: Руководство. Интеграция Azure Active Directory с Amazon Web Services (AWS) | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e49bc5a468777197eaf88a492566a606e7b9f93
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961879"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Руководство. Интеграция Azure Active Directory с Amazon Web Services (AWS)

В этом руководстве описано, как интегрировать приложение Amazon Web Services (AWS) с Azure Active Directory (Azure AD).
Интеграция Azure AD с приложением Amazon Web Services обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Amazon Web Services.
* Вы можете включить автоматический вход пользователей в Amazon Web Services (AWS) (единый вход) под учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно на портале Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Единый вход в приложениях в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

![Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Вы можете настроить идентификаторы для нескольких экземпляров, как показано ниже.

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

С помощью этих значений Azure AD будет удалять значение **#** и отправлять правильное значение `https://signin.aws.amazon.com/saml` в качестве URL-адреса аудитории в токене SAML.

**Мы рекомендуем использовать этот подход по следующим причинам.**

a. Каждое приложение будет предоставлять уникальный сертификат X509, поэтому у экземпляров могут быть разные даты окончания срока действия, и вы сможете управлять ими отдельно для каждой учетной записи AWS. В данном случае процесс смены сертификатов будет проще.

b. Вы можете включить подготовку пользователей с помощью приложения AWS в Azure AD, после чего наша служба будет получать все роли из этой учетной записи AWS. Не нужно вручную добавлять или обновлять роли AWS для приложения.

c. Вы можете назначить владельца приложения единственным пользователем приложения, который может управлять этим приложением непосредственно в Azure AD.

> [!Note]
> Убедитесь, что используется только приложение из коллекции.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Amazon Web Services, вам потребуется следующее:

* подписка Azure AD (если у вас нет среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/));
* Подписка с поддержкой единого входа в Amazon Web Services (AWS)

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

> [!Note]
> Если вы хотите интегрировать несколько учетных записей AWS в одну учетную запись Azure для единого входа, обратитесь к [этой](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial) статье.

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства вы настроите и проверите единый вход Azure AD в тестовой среде.

* Amazon Web Services (AWS) поддерживает единый вход, инициированный **поставщиком услуг и поставщиком удостоверений**.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Добавление Amazon Web Services из коллекции

Чтобы настроить интеграцию Amazon Web Services с Azure AD, вам потребуется добавить Amazon Web Services из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Amazon Web Services (AWS) из коллекции, выполните следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Amazon Web Services (AWS)**, выберите **Amazon Web Services (AWS)** на панели результатов, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

     ![Amazon Web Services (AWS) в списке результатов](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Amazon Web Services (AWS) с использованием тестового пользователя **Britta Simon**.
Для обеспечения работы единого входа необходимо установить связь между пользователем Azure Active Directory и соответствующим пользователем Amazon Web Services (AWS).

Чтобы настроить и проверить единый вход Azure AD в Amazon Web Services, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Настройка единого входа в Amazon Web Services (AWS)](#configure-amazon-web-services-aws-single-sign-on)** необходима, чтобы настроить параметры единого входа на стороне приложения.
3. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы разрешить пользователю Britta Simon использовать единый вход Azure AD.
5. **[Создание тестового пользователя Amazon Web Services (AWS)](#create-amazon-web-services-aws-test-user)** требуется для создания соответствующего пользователя Britta Simon в Amazon Web Services (AWS), связанного с одноименным пользователем в Azure AD.
6. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы проверить работу конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано включение единого входа Azure AD на портале Azure.

Чтобы настроить единый вход Azure AD в Amazon Web Services, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com/) на странице интеграции с приложением **Amazon Web Services (AWS)** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"](common/select-sso.png)

2. В диалоговом окне **Выбрать метод единого входа** выберите режим **SAML/WS-Fed**, чтобы включить единый вход.

    ![Режим выбора единого входа](common/select-saml-option.png)

3. На странице **Настройка единого входа с помощью SAML** щелкните **Изменить**, чтобы открыть диалоговое окно **Базовая конфигурация SAML**.

    ![Правка базовой конфигурации SAML](common/edit-urls.png)

4. В разделе **Базовая конфигурация SAML** не нужно выполнять никаких действий, так как приложение уже предварительно интегрировано с Azure.  Выберите команду **Сохранить**.

    ![изображение](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

5. При настройке более одного экземпляра следует указывать значение идентификатора. Для второго и последующих экземпляров следует указать значение идентификатора в приведенном ниже формате. Используйте знак **#**, чтобы указать уникальное значение имени субъекта-службы.

    `https://signin.aws.amazon.com/saml#2`

    ![Сведения о домене и URL-адресах для единого входа в приложение Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_identifier.png)

6. Приложение Amazon Web Services (AWS) ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На странице **Настройка единого входа с помощью SAML** нажмите кнопку **Изменить**, чтобы открыть диалоговое окно **Атрибуты пользователя**.

    ![изображение](common/edit-attribute.png)

7. В разделе **Утверждения пользователя** диалогового окна **Атрибуты пользователя** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.

    | ИМЯ  | Исходный атрибут  | Пространство имен |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Роль            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "Укажите значение от 900 секунд (15 минут) до 43 200 секунд (12 часов)." |  https://aws.amazon.com/SAML/Attributes |

    a. Щелкните **Добавить новое утверждение**, чтобы открыть диалоговое окно **Управление утверждениями пользователя**.

    ![изображение](common/new-save-attribute.png)

    ![изображение](common/new-attribute-details.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В текстовом поле **Пространство имен** введите значение пространства имен, показанное для этой строки.

    4.3. В качестве источника выберите **Атрибут**.

    д. В списке **Атрибут источника** введите значение атрибута, отображаемое для этой строки.

    Е. Нажмите кнопку **ОК**.

    ж. Выберите команду **Сохранить**.

8. На странице **Настройка единого входа с помощью SAML** в разделе **Сертификат подписи SAML** щелкните **Скачать**, чтобы скачать нужный вам **XML метаданных федерации**, и сохраните его на компьютере.

    ![Ссылка для скачивания сертификата](common/metadataxml.png)

9. Скопируйте требуемые URL-адреса из раздела **Настройка Amazon Web Services (AWS)**.

    ![Копирование URL-адресов настройки](common/copy-configuration-urls.png)

    а) URL-адрес входа.

    б) Идентификатор Azure AD.

    в) URL-адрес выхода.

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Настройка единого входа в Amazon Web Services (AWS)

1. В другом окне браузера войдите на сайт Amazon Web Services компании в качестве администратора.

2. Щелкните **AWS Home** (Домашняя страница AWS).

    ![Настройка главной страницы единого входа][11]

3. Щелкните **Identity and Access Management**(Управление удостоверениями и доступом).

    ![Настройка удостоверения для единого входа][12]

4. Щелкните **Identity Providers** (Поставщики удостоверений), затем щелкните **Create Provider** (Создать поставщик).

    ![Настройка поставщика единого входа][13]

5. На странице диалогового окна **Configure Provider** (Настройка поставщика) выполните следующие действия.

    ![Настройка диалогового окна единого входа][14]

    a. Выберите для параметра **Provider Type** (Тип поставщика) значение **SAML**.

    b. В текстовом поле **Имя поставщика** введите имя поставщика (например, *WAAD*).

    c. Чтобы отправить **файл метаданных**, скачанный с портала Azure, нажмите кнопку **Choose File** (Выбрать файл).

    4.3. Щелкните **Следующий шаг**.

6. На странице диалогового окна **Verify Provider Information** (Проверка сведений о поставщике) щелкните **Create** (Создать).

    ![Настройка проверки единого входа][15]

7. Щелкните **Roles** (Роли), а затем нажмите кнопку **Create role** (Создать роль).

    ![Настройка ролей для единого входа][16]

8. На странице **Create role** (Создание роли) выполните следующие действия.  

    ![Настройка доверия для единого входа][19]

    a. В разделе **Select type of trusted entity** (Выберите тип доверенной сущности) выберите **SAML 2.0 federation** (Федерация SAML 2.0).

    b. В разделе **Choose a SAML 2.0 Provider** (Выберите поставщик SAML 2.0) выберите **поставщик SAML**, созданный ранее (например, *WAAD*).

    c. Установите флажок **Allow programmatic and AWS Management Console access** (Разрешить программный доступ и доступ через консоль управления AWS).
  
    4.3. Щелкните **Далее: разрешения**.

9. В диалоговом окне **Attach Permissions Policies** (Вложить политики разрешений) вложите соответствующую политику согласно вашей организации. Щелкните **Далее: Review** (Далее: проверка).  

    ![Настройка политики единого входа][33]

10. В диалоговом окне **Review** (Обзор) выполните следующие действия.

    ![Проверка настройки единого входа][34]

    a. В текстовом поле **Role name** (Имя роли) введите имя роли.

    b. В текстовом поле **Role description** (Описание роли) введите описание.

    c. Щелкните **Создать роль**.

    4.3. Создайте необходимое количество ролей и сопоставьте их с поставщиком удостоверений.

11. Используйте учетные данные службы AWS для получения ролей из учетной записи AWS при подготовке пользователя Azure AD. Для этого откройте главную страницу консоли AWS.

12. Щелкните **Services (Службы)** -> **Security, Identity & Compliance (Безопасность, удостоверения и соответствие требованиям)** -> **IAM**.

    ![Получение ролей из учетной записи AWS](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Выберите вкладку **Policies** (Политики) в разделе IAM.

    ![Получение ролей из учетной записи AWS](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Щелкнув **Create policy** (Создать политику), создайте политику для получения ролей из учетной записи AWS при подготовке пользователей Azure AD.

    ![Создание новой политики](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Создайте собственную политику для получения всех ролей из учетных записей AWS, выполнив следующие действия.

    ![Создание новой политики](./media/amazon-web-service-tutorial/policy1.png)

    a. В разделе **Создание политики** щелкните вкладку **JSON**.

    b. Добавьте приведенный ниже код JSON в документ политики.

    ```

    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",

    "Action": [

    "iam:ListRoles"

    ],

    "Resource": "*"

    }

    ]

    }

    ```

    c. Нажмите кнопку **Просмотр политики**, чтобы проверить политику.

    ![Определение новой политики](./media/amazon-web-service-tutorial/policy5.png)

16. Определите **новую политику**, выполнив следующие действия.

    ![Определение новой политики](./media/amazon-web-service-tutorial/policy2.png)

    a. В поле **Policy Name** (Имя политики) введите **AzureAD_SSOUserRole_Policy**.

    b. Укажите описание политики в поле **Description**: **Эта политика позволит получать роли из учетных записей AWS**.

    c. Нажмите кнопку **Создать политику**.

17. Создайте новую учетную запись пользователя в службе AWS IAM, выполнив следующие действия.

    a. Щелкните пункт **Users** (Пользователи) в области навигации консоли AWS IAM.

    ![Определение новой политики](./media/amazon-web-service-tutorial/policy3.png)

    b. Нажмите кнопку **Add user** (Добавить пользователя), чтобы создать нового пользователя.

    ![Добавление пользователя](./media/amazon-web-service-tutorial/policy4.png)

    c. В разделе **Add user** (Добавление пользователя) выполните следующие действия.

    ![Добавление пользователя](./media/amazon-web-service-tutorial/adduser1.png)

    * Введите имя пользователя **AzureADRoleManager**.

    * В разделе Access type (Тип доступа) выберите вариант **Programmatic access** (Программный доступ). Таким образом пользователь сможет вызывать API-интерфейсы и получать роли из учетной записи AWS.

    * Нажмите кнопку **Next Permissions** (Следующие разрешения) в правом нижнем углу.

18. Теперь создайте новую политику для этого пользователя, выполнив следующие действия.

    ![Добавление пользователя](./media/amazon-web-service-tutorial/adduser2.png)

    a. Нажмите кнопку **Attach existing policies directly** (Подключить существующие политики напрямую).

    b. Выполните поиск только что созданной политики в разделе фильтра **AzureAD_SSOUserRole_Policy**.

    c. Выберите **политику**, а затем нажмите кнопку **Next: Review** (Далее: просмотр).

19. Просмотрите политику для подключенного пользователя, выполнив следующие действия.

    ![Добавление пользователя](./media/amazon-web-service-tutorial/adduser3.png)

    a. Проверьте имя пользователя, тип доступа и политику, сопоставленную с пользователем.

    b. Нажмите кнопку **Create user** (Создать пользователя) в правом нижнем углу, чтобы создать пользователя.

20. Скачайте учетные данные пользователя, выполнив следующие действия.

    ![Добавление пользователя](./media/amazon-web-service-tutorial/adduser4.png)

    a. Скопируйте значения полей **Access key ID** (Идентификатор ключа доступа) и **Secret access key** (Секретный ключ доступа) для этого пользователя.

    b. Введите эти учетные данные в раздел подготовки пользователя Azure AD, чтобы получить роли из консоли AWS.

    c. В нижней части экрана нажмите кнопку **Close** (Закрыть).

21. Перейдите к разделу **User Provisioning** (Подготовка пользователя) приложения Amazon Web Services на портале управления Azure AD.

    ![Добавление пользователя](./media/amazon-web-service-tutorial/provisioning.png)

22. Введите **ключ доступа** и **секрет** в поля **Секрет клиента** и **Секретный токен** соответственно.

    ![Добавление пользователя](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Введите ключ доступа пользователя AWS в поле **Секрет клиента**.

    b. Введите секрет пользователя AWS в поле **Секретный токен**.

    c. Нажмите кнопку **Проверить подключение**, чтобы успешно протестировать подключение.

    4.3. Сохраните настройку, нажав кнопку **Сохранить** вверху.

23. Теперь **включите** параметр "Состояние подготовки" в разделе "Параметры" и нажмите кнопку **Сохранить** вверху.

    ![Добавление пользователя](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

1. На портале Azure в области слева выберите **Azure Active Directory**, **Пользователи**, а затем — **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](common/users.png)

2. В верхней части экрана выберите **Новый пользователь**.

    ![Кнопка "Новый пользователь"](common/new-user.png)

3. В разделе свойств пользователя сделайте следующее:

    ![Диалоговое окно "Пользователь"](common/user-properties.png)

    а. В поле **Имя** введите **BrittaSimon**.
  
    b. В поле **Имя пользователя** введите **brittasimon@yourcompanydomain.extension**.  
    Например BrittaSimon@contoso.com.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле "Пароль".

    d. Нажмите кнопку **Создать**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Amazon Web Services (AWS), чтобы этот пользователь мог использовать единый вход Azure.

1. На портале Azure выберите **Корпоративные приложения**, **Все приложения**, а затем — **Amazon Web Services (AWS)**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений введите и выберите **Amazon Web Services (AWS)**.

    ![Ссылка на Amazon Web Services (AWS) в списке приложений](common/all-applications.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"](common/users-groups-blade.png)

4. Нажмите кнопку **Добавить пользователя**, а затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"](common/add-assign-user.png)

5. В диалоговом окне **Пользователи и группы** из списка пользователей выберите **Britta Simon**, а затем в верхней части экрана нажмите кнопку **Выбрать**.

6. Если ожидается, что в утверждении SAML будет получено какое-либо значение роли, то в диалоговом окне **Выбор ролей** нужно выбрать соответствующую роль для пользователя из списка и затем нажать кнопку **Выбрать**, расположенную в нижней части экрана.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="create-amazon-web-services-aws-test-user"></a>Создание тестового пользователя Amazon Web Services (AWS)

Цель этого раздела — создать пользователя с именем Britta Simon в Amazon Web Services. Для единого входа в системе служб Amazon Web Services (AWS) не требуется создавать пользователя, поэтому здесь не нужно выполнять никаких действий.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Amazon Web Services (AWS) на панели доступа, вы автоматически войдете в приложение Amazon Web Services (AWS), для которого настроили единый вход. См. дополнительные сведения о [панели доступа](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="known-issues"></a>Известные проблемы

 * В разделе **Подготовка** подраздел **Сопоставление** будет отображать сообщение "Идет загрузка..." и никогда не отображать сопоставления атрибутов. Единственный рабочий процесс подготовки, поддерживаемый сегодня, — импорт ролей из AWS в Azure AD для выбора во время назначения пользователя или группы. Сопоставления атрибутов для этого определены заранее и не настраиваются.
 
 * Раздел **Подготовка** поддерживает ввод только одного набора учетных данных для одного клиента AWS за раз. Все импортированные роли записываются в свойство appRoles [объекта servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) Azure AD для клиента AWS. Из коллекции для подготовки в Azure AD можете добавить множество клиентов AWS (представленных объектами servicePrincipal), однако существует проблема с невозможностью автоматического записывания всех импортированных ролей из многих объектов servicePrincipal AWS, используемых для подготовки единого servicePrincipal для единого входа. Чтобы избежать этого, [API Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) может использоваться для извлечения всех appRoles, импортированных в каждый объект servicePrincipal AWS, где настроена подготовка. Эти строки ролей впоследствии могут добавляться к servicePrincipal AWS, где настроен единый вход.

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Список учебников по интеграции приложений SaaS с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Что представляет собой условный доступ в Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
