---
title: Руководство. Azure Databricks Настройка соединителя SCIM для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как автоматически подготавливать и отзывать учетные записи пользователей из Azure AD в Azure Databricks соединителя SCIM.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b16eaf27-4bd1-4509-be2c-9a4f66b6badc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2020
ms.author: Zhchia
ms.openlocfilehash: de60b4ea1b09998e84bab4d204e3c8c3bc8779a4
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77050451"
---
# <a name="tutorial-configure-azure-databricks-scim-connector-for-automatic-user-provisioning"></a>Руководство. Azure Databricks Настройка соединителя SCIM для автоматической подготовки пользователей

В этом руководстве описываются действия, которые необходимо выполнить в Azure Databricks соединителе SCIM и Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. После настройки Azure AD автоматически подготавливает и отменяет подготовку пользователей и групп для [Azure Databricks СОЕДИНИТЕЛЯ scim](https://databricks.com/) с помощью службы подготовки Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей в Azure Databricks соединителе SCIM
> * Удалить пользователей в Azure Databricks соединителе SCIM, когда им больше не нужен доступ
> * Синхронизация атрибутов пользователей между Azure AD и Azure Databricks соединителем SCIM
> * Подготавливайте группы и членство в группах в соединителе Azure Databricks SCIM

## <a name="prerequisites"></a>предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD;](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор). 
* Учетная запись Azure Databricks с разрешениями администратора.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания подготовки
1. Узнайте [, как работает служба подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Определите, кто будет находиться в [области подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Определите, какие данные должны [сопоставляться между Azure AD и Azure Databricks соединителем scim](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-azure-databricks-scim-connector-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка соединителя Azure Databricks SCIM для поддержки подготовки с помощью Azure AD

1. Чтобы настроить Azure Databricks SCIM, добавьте его в качестве ресурса в свой клиент Azure Active Directory и настройте его, используя приведенные ниже параметры.

    ![Установка Azure Databricks](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/setup.png)

2. Чтобы создать личный маркер доступа в Azure Databricks обратитесь к [этой](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/authentication#token-management)статье.

3. Скопируйте **токен**. Это значение будет указано в поле Секретный токен на вкладке Подготовка приложения Azure Databricksного соединителя SCIM в портал Azure.

## <a name="step-3-add-azure-databricks-scim-connector-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление Azure Databricks соединителя SCIM из коллекции приложений Azure AD

Добавьте Azure Databricks соединителя SCIM из коллекции приложений Azure AD, чтобы начать управление подготовкой для Azure Databricks соединителя SCIM. Если вы ранее настроили Azure Databricks соединителя SCIM для единого входа, вы можете использовать то же приложение. Однако при первоначальном тестировании интеграции рекомендуется создать отдельное приложение. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей, которые будут находиться в области подготовки 

Служба подготовки Azure AD позволяет определить, кто будет подготовлен в соответствии с назначением приложения, или на основе атрибутов пользователя или группы. Если вы решили указать, кто будет подготовлен для приложения на основе назначения, можно выполнить следующие [действия](../manage-apps/assign-user-or-group-access-portal.md) , чтобы назначить пользователей и группы для приложения. Если выбрать область, для которой будет выполняться подготовка на основе только атрибутов пользователя или группы, можно использовать фильтр области, как описано [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* При назначении пользователей и групп для Azure Databricks соединителя SCIM необходимо выбрать роль, отличную от **доступа по умолчанию**. Пользователи с ролью доступа по умолчанию исключаются из подготовки и будут помечены как неэффективное в журналах подготовки. Если единственной ролью, доступной в приложении, является роль доступа по умолчанию, можно [обновить манифест приложения](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) , чтобы добавить дополнительные роли. 

* Запуск Small. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если для параметра область подготовки задано значение назначенные пользователи и группы, это можно контролировать, назначив приложению одного или двух пользователей или групп. Если для параметра scope задано значение все пользователи и группы, можно указать [Фильтр области на основе атрибутов](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-azure-databricks-scim-connector"></a>Шаг 5. Настройка автоматической подготовки пользователей для Azure Databricks соединителя SCIM 

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и групп в TestApp на основе назначений пользователей и групп в Azure AD.

> [!NOTE]
> Дополнительные сведения о конечной точке SCIM для Azure Databricks см. [здесь](https://docs.databricks.com/dev-tools/api/latest/scim.html
).

### <a name="to-configure-automatic-user-provisioning-for-azure-databricks-scim-connector-in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей для соединителя Azure Databricks SCIM в Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Azure Databricks соединитель scim**.

    ![Ссылка на Azure Databricks SCIM Connector в списке "приложения"](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Вкладка "подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Вкладка "подготовка"](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите учетные данные администратора Azure Databricks scim Connector и имя пользователя. Нажмите кнопку **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к соединителю Azure Databricks scim. В случае сбоя подключения убедитесь, что у учетной записи соединителя Azure Databricks SCIM есть разрешения администратора, и повторите попытку.

    ![Подготовка](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/provisioning.png)

6. В поле **уведомление по электронной почте** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, и установите флажок **Отправить уведомление по электронной почте при возникновении сбоя** .

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Щелкните **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с Azure DATABRICKS соединителем scim**.

9. Ознакомьтесь с атрибутами пользователей, которые синхронизированы из Azure AD, с Azure Databricks соединителем SCIM в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в Azure Databricks соединителе scim для операций обновления. Если вы решили изменить [соответствующий целевой атрибут](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), необходимо убедиться, что API СОЕДИНИТЕЛЯ Azure Databricks scim поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

   |attribute|Тип|
   |---|---|
   |userName|String|
   |displayName|String|
   |active|Логическое|

10. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory группы для Azure Databricks соединителя scim**.

11. Проверьте атрибуты группы, которые синхронизированы из Azure AD, чтобы Azure Databricks соединитель SCIM в разделе **сопоставления атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления групп в Azure Databricks соединителе scim для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

     |attribute|Тип|
     |---|---|
     |displayName|String|
     |members|Справочник|

11. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory группы для Azure Databricks соединителя scim**.

12. Чтобы включить службу подготовки Azure AD для Azure Databricks соединителя SCIM, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

13. Определите пользователей и (или) группы, которые вы хотите подготавливать для Azure Databricks соединителя SCIM, выбрав нужные значения в поле **область** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

14. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

Эта операция запускает начальный цикл синхронизации всех пользователей и групп, определенных в **области** в разделе **параметров** . Выполнение начального цикла занимает больше времени, чем последующие циклы, что происходит примерно каждые 40 минут, пока выполняется служба подготовки Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания:

* Используйте [журналы подготовки](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , чтобы определить, какие пользователи были подготовлены успешно или неудачно
* Просмотрите [индикатор выполнения](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , чтобы просмотреть состояние цикла подготовки и его завершения
* Если конфигурация подготовки, вероятно, находится в неработоспособном состоянии, приложение перейдет в карантин. Дополнительные сведения о состояниях карантина см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Советы по устранению неполадок
* Модуль данных всегда преобразует свои значения имени пользователя в нижний регистр при сохранении в своем каталоге независимо от регистра букв, отправляемых им через SCIM.
* В настоящее время запросы GET к Azure Databricks API SCIM для пользователей чувствительны к регистру, поэтому при запросе USER@contoso.com он будет иметь 0 результатов, так как он сохраняется как user@contoso.com.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
