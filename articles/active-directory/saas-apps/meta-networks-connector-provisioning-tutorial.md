---
title: 'Учебник: Наконкажирование метасетей разъем для автоматического подготовки пользователей с помощью Active Directory Azure (ru) Документы Майкрософт'
description: Узнайте, как настроить Active Directory Azure для автоматического предоставления и де-предоставления учетных записей пользователей в Meta Networks Connector.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 6e34e540-7761-4bdf-a2f9-8c12becc119d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 03c2dc6253fba5c2c7d59f3aefc5c1c663ed8248
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061366"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Учебник: Наконказначайте метасети Разъем для автоматического подготовки пользователей

Цель этого руководства состоит в том, чтобы продемонстрировать шаги, которые должны быть выполнены в Meta Networks Connector и Azure Active Directory (Azure AD) для настройки Azure AD для автоматического предоставления и де-предоставления пользователям и/или группам в Meta Networks Connector.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Арендатор мета сети соединитель](https://www.metanetworks.com/)
* Учетная запись пользователя в meta Networks Connector с разрешениями Admin.

## <a name="assigning-users-to-meta-networks-connector"></a>Назначение пользователей в разъем Meta Networks

Активный каталог Azure использует концепцию, называемую *назначениями,* чтобы определить, какие пользователи должны получить доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи и/или группы, назначенные приложению azure AD.

Прежде чем настроить и включить автоматическую подготовку пользователей, необходимо решить, какие пользователи и/или группы в Azure AD нуждаются в доступе к разъединению Meta Networks. После решения, вы можете назначить этих пользователей и / или групп мета сетей разъем, следуя инструкциям здесь:
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Важные советы по назначению пользователей в Meta Networks Connector

* Для тестирования автоматической конфигурации подготовки пользователей рекомендуется назначить одного пользователя Azure AD в разъем Meta Networks. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в "Мета сети- разъем" необходимо выбрать любую действительную роль приложения (если она имеется) в диалоге заданий. Пользователи с ролью **доступа по умолчанию** исключены из подготовки.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Установка Мета сети Разъем для подготовки

1. Воссоединитесь с [адъюминкой Meta Networks,](https://login.metanetworks.com/login/) используя название организации. Перейдите к **administration > API Keys**.

    ![Мета сети Разъем Админ консоль](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Нажмите на знак плюс в верхней правой части экрана, чтобы создать новый **ключ API.**

    ![Мета сети Разъем плюс значок](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Установите **имя ключа API** и **описание ключа API**.

    ![Мета сети Разъем Создать маркер](media/meta-networks-connector-provisioning-tutorial/keyname.png)

4.  Включите привилегии **Write** для **групп** и **пользователей.**

    ![Привилегии мета сети соединитель](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Нажмите на **добавить**. Копировать **SECRET** и сохранить его, как это будет единственный раз, когда вы можете просмотреть его. Это значение будет введено в поле Secret Token в вкладке «Подготовка» приложения Meta Networks Connector на портале Azure.

    ![Мета сети Разъем Создать маркер](media/meta-networks-connector-provisioning-tutorial/token.png)

6.  Добавьте IdP, пересягнув в **настройки администрирования > > IdP > создайте новые.**

    ![Мета сети Разъем Добавить IdP](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  На странице **Конфигурации IdP** вы можете **назвать** конфигурацию IdP и выбрать **значок.**

    ![Мета сети Соединитель IdP Имя](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Мета сети Соединитель IdP значок](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  Под **настройкой SCIM** выберите ключевое имя API, созданное в предыдущих шагах. Нажмите на **Сохранить**.

    ![Мета сети Разъем настройки SCIM](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Перейдите к **настройкам > администрирования > вкладке IdP.** Нажмите на имя конфигурации IdP, созданной в предыдущих шагах, чтобы просмотреть **idP.** Этот **идентификатор** добавляется к концу **URL-адреса арендатора** при вводе значения в поле **URL-адреса арендатора** в вкладке «Подготовка» приложения «Подъемике сетей Мета» на портале Azure.

    ![Мета Сети Соединитель IdP ID](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Добавить Мета Сети Коннектор из галереи

Прежде чем настроить подъезжающий к Мета сетям для автоматического оформления пользовательского интерфейса с помощью Azure AD, необходимо добавить в список управляемых приложений SaaS разъем Meta Networks из галереи приложений Azure AD.

**Чтобы добавить разъем Meta Networks из галереи приложений Azure AD, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** на левой навигационной панели выберите **Active Directory Azure.**

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите к **корпоративным приложениям,** а затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите кнопку **Нового приложения** в верхней части панели.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **разъем Meta Networks,** выберите **разъем Meta Networks** в панели результатов, а затем нажмите кнопку **Добавить,** чтобы добавить приложение.

    ![Meta Networks Connector в списке результатов](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Настройка автоматического подключения пользователей к разъединению Meta Networks 

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отключения пользователей и/или групп в подъемике Meta Networks на основе пользовательских и/или групповых заданий в Azure AD.

> [!TIP]
> Вы также можете включить SAML основе одного знака для Мета Сети Разъем , следуя инструкциям, представленным в [Meta Networks Connector Единый набор.](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial) Единый подгон может быть настроен независимо от автоматического подготовки пользователей, хотя эти две функции дополняют друг друга

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Для настройки автоматического подключения пользователей к разъединению Meta Networks в Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Meta Networks Connector**.

    ![Ссылка на Meta Networks Connector в списке "Приложения"](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **Admin Credentials** `https://api.metanetworks.com/v1/scim/<IdP ID>` ввод **URL-адреса клиента**. Ввейте значение **токенов подлинности SCIM,** извлеченное ранее в **Secret Token.** Нажмите **на тестовое соединение,** чтобы обеспечить подключение Azure AD к разъединению Meta Networks. Если подключение не удается, убедитесь, что ваша учетная запись Meta Networks Connector имеет разрешения Admin и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure в подъемите meta Networks.**

    ![Мета сети Соединитель Пользовательские картографы](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Просмотрите атрибуты пользователя, синхронизированные с Azure AD с разъемом Meta Networks в разделе **Атрибуткарт.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в разъеме Meta Networks для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты пользователя Мета Сетей подключения](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. В разделе **Картпинги** выберите **синхронизацию активных групп каталогов Azure в подъемике meta Networks.**

    ![Мета Сети Соединитель группы Карты](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Просмотрите атрибуты группы, синхронизированные с Azure AD с разъемом Meta Networks в разделе **Атрибуткарт.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления групп в разъеме Meta Networks для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты группы мета сетей](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу предоставления Azure AD для соединения метасетей, измените **статус подготовки** **в** разделе **«Настройки».**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и/или группы, которые вы хотели бы предоставить в Meta Networks Connector, выбрав желаемые значения в **разделе Область** в разделе **Параметры.**

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Раздел **«Детали синхронизации»** можно использовать для мониторинга прогресса и отслеживания ссылок на отчет о деятельности, в котором описаны все действия, выполняемые службой предоставления AD Azure AD на разъединении Meta Networks.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)

