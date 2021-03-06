---
title: Учебник. Настройка трубки для автоматической подготовки пользователей с Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической подготовки и отмены подготовки учетных записей пользователей в трубки.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 32e634bc089417aaa8080b30a5f77f663a3d8b33
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611780"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Учебник. Настройка трубки для автоматической подготовки пользователей

Цель данного учебника — продемонстрировать действия, выполняемые в трубки и Azure Active Directory (Azure AD) для настройки Azure AD для автоматической подготовки и отзыва пользователей и групп в трубки.

> [!NOTE]
>  В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).

> Сейчас этот соединитель доступен в предварительной версии. Дополнительные сведения о общие Microsoft Azure условия использования предварительных версий функций, см. в разделе [дополнительным условиям использования предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* Клиент Azure AD.
* [Клиент трубки](https://www.dialpad.com/pricing/).
* Учетная запись пользователя в трубки с разрешениями администратора.

## <a name="assign-users-to-dialpad"></a>Назначить пользователей трубки
Azure Active Directory используется концепция, называемая назначений, чтобы определить, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только те пользователи и группы, которые были назначены приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей, следует решить, какие пользователи или группы в Azure AD требуется доступ к трубки. Сделав это, можно назначить этих пользователей и групп, чтобы трубки следуя приведенным ниже указаниям:
 
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Важные рекомендации по назначению пользователей в окно набора номера

 * Рекомендуется одного пользователя Azure AD назначается трубки для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в окно набора номера, необходимо выбрать действительную роль конкретного приложения (если доступно) в диалоговом окне назначения. Пользователи с ролью доступа по умолчанию исключаются из подготовки.

## <a name="setup-dialpad-for-provisioning"></a>Программа установки трубки для подготовки

Перед настройкой трубки для автоматической подготовки пользователей в Azure AD, необходимо будет получить некоторые сведения о подготовке из трубки.

1. Войдите в ваш [консоли администрирования трубки](https://dialpadbeta.com/login) и выберите **параметры администратора**. Убедитесь, что **My Company** выбирается из раскрывающегося списка. Перейдите к **проверки подлинности > ключи API**.

    ![Трубки добавить SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Создать новый ключ, щелкнув **добавьте раздел** и настройка свойств секрета маркера.

    ![Трубки добавить SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Трубки добавить SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Нажмите кнопку **щелкните, чтобы показать значение** кнопку свой последний созданный ключ API и скопируйте значение показано. Это значение будет указываться в **секретный токен** в вкладке "Подготовка" трубки приложения на портале Azure. 

    ![Трубки создание токена](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Добавить трубки из коллекции

Чтобы настроить окно набора номера для автоматической подготовки пользователей в Azure AD, необходимо добавить трубки из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить трубки из коллекции приложений Azure AD, выполните следующие действия:**

1. В  **[портала Azure](https://portal.azure.com)** , на панели навигации слева выберите **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите **новое приложение** кнопку в верхней части области.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **трубки**выберите **трубки** на панели результатов.
    ![Окно набора номера, в списке результатов](common/search-new-app.png)

5. Перейдите к **URL-адрес** выделенный ниже, в отдельный браузер. 

    ![Трубки добавить SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. В правом верхнем углу выберите **вход > Используйте трубки online**.

    ![Трубки добавить SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Как трубки OpenIDConnect приложения, выберите в систему, чтобы окно набора номера, используя рабочую учетную запись Майкрософт.

    ![Трубки добавить SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. После успешной проверки подлинности примите запрос согласия для страница согласия. Приложения будут автоматически добавляться к вашему клиенту, и вы будете перенаправлены к учетной записи трубки.

    ![Трубки добавить SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Настройка автоматической подготовки пользователей для трубки

В этом разделе поможет выполнить действия, чтобы настроить службу подготовки Azure AD для создания, изменения и отключения пользователей и групп в трубки на основе назначений в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Настройка автоматической подготовки пользователей для трубки в Azure AD.

1. Войдите на [портале Azure](https://portal.azure.com). Выберите **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **трубки**.

    ![Ссылка на окно набора номера в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Инициализация](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Инициализация](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите `https://dialpad.com/scim` в **URL-адрес клиента**. Входные данные, полученные и сохраненные ранее из трубки в значение **секретный токен**. Нажмите кнопку **проверить подключение** и убедиться, что Azure AD может подключиться к трубки. Если подключение отсутствует, убедитесь, что учетная запись трубки имеет разрешения администратора и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Уведомление по электронной почте](common/provisioning-notification-email.png)

7. Нажмите кнопку **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать пользователей Azure Active Directory с трубки**.

    ![Сопоставления пользователей трубки](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в трубки в **сопоставление атрибутов** раздел. Атрибуты, выбранные как **Matching** свойства используются для сопоставления учетных записей пользователей в трубки для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты пользователя трубки](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Чтобы включить службу для трубки подготовки Azure AD, измените **состояние подготовки** для **на** в **параметры** раздел.

    ![Состояние подготовки на противоположное](common/provisioning-toggle-on.png)

12. Определите пользователей или группы, которые вы хотите для подготовки в окно набора номера, выбрав нужные значения в **область** в **параметры** раздел.

    ![Инициализация области](common/provisioning-scope.png)

13. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Идет сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Можно использовать **сведения о синхронизации** раздел, чтобы отслеживать ход выполнения и переходить по ссылкам для подготовки отчетов действия, в которых зафиксированы все действия, выполняемые со службой подготовки Azure AD.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в разделе [отчетов об автоматической подготовке учетных записей](../manage-apps/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Ограничения соединителя
* Окно набора номера не поддерживает переименование группы уже сегодня. Это означает, что любые изменения **displayName** группы в Azure AD будет не обновлена и отражаются в трубки.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Следующие шаги

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
