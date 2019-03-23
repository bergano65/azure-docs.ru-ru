---
title: Согласие администратора для подключения к учетной записи LinkedIn — Azure Active Directory | Документация Майкрософт
description: Объясняется, как включить или отключить подключения к учетной записи интеграции LinkedIn в приложениях Майкрософт в Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e07c53192ea2c8b792256af944c81c9c909dc55
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368132"
---
# <a name="consent-to-linkedin-account-connections-for-an-azure-active-directory-organization"></a>Согласие для подключений к учетной записи LinkedIn для организации Azure Active Directory

Можно разрешить пользователям в вашей организации для доступа к их подключений LinkedIn в некоторых приложениях Майкрософт. Данные не используется совместно, пока пользователи разрешать подключения учетной записи. Вы можете интегрировать вашей организации в Azure Active Directory (Azure AD) [центра администрирования](https://aad.portal.azure.com).

> [!IMPORTANT]
> Установка подключения учетной записи LinkedIn в настоящее время разворачивается для организаций, Azure AD. Если она внедрена для вашей организации, он включается по умолчанию.
> 
> Исключения:
> * Этот параметр недоступен для клиентов, использующих Microsoft Cloud for US Government, Microsoft Cloud Germany, а также служб Azure и Office 365, обслуживаемых 21Vianet в Китае.
> * Этот параметр по умолчанию отключен для клиентов, подготовленных в Германии. Обратите внимание, что этот параметр недоступен для клиентов, использующих Microsoft Cloud Germany.
> * Этот параметр по умолчанию отключен для клиентов, подготовленных во Франции.
>
> После включения подключений к учетной записи LinkedIn для вашей организации подключения к учетным записям работать после пользователям разрешать приложениям доступ к данным компании от своего имени. Сведения о настройке согласия пользователя, см. в разделе [об удалении доступа пользователя к приложению](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="use-the-azure-portal-to-enable-linkedin-account-connections"></a>Чтобы включить подключения к учетной записи LinkedIn с помощью портала Azure

Вы можете включить подключения к учетной записи LinkedIn для пользователей, которые вы хотите иметь доступ из всей организации только выбранным пользователям в вашей организации.

1. Войдите в [Центр администрирования Azure AD](https://aad.portal.azure.com/) с учетной записью, которая является глобальным администратором для организации Azure AD.
2. Выберите **Пользователи**.
3. В колонке **Пользователи** выберите **Параметры пользователей**.
4. В разделе **подключений к учетной записи LinkedIn**, разрешить пользователям подключать учетные записи для доступа к их подключений LinkedIn в некоторых приложениях Майкрософт. Данные не используется совместно, пока пользователи разрешать подключения учетной записи.

  * Выберите **Да** согласие на службы для всех пользователей в организации
  * Выберите **выбранные** принять это разрешение только для выбранных пользователей в организации
  * Выберите **нет** отказаться от получения согласия для пользователей в вашей организации

    ![Интеграция подключений к учетной записи LinkedIn в организации](./media/linkedin-integration/linkedin-integration.png)

5. Когда все будет готово, выберите **Сохранить** для сохранения параметров.
     
## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Чтобы включить подключения к учетной записи LinkedIn с помощью групповой политики

1. Скачайте [файлы административных шаблонов Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030).
2. Извлеките файлы **ADMX** и скопируйте их в центральное хранилище.
3. Откройте средство управления групповыми политиками.
4. Создайте объект групповой политики со следующими параметрами: **Конфигурация пользователя** > **Административные шаблоны** > **Microsoft Office 2016** > **Разное** > **Показывать функции LinkedIn в приложениях Office**.
5. Выберите **Включено** или **Отключено**.
  
   Состояние | Результат
   ------ | ------
   **Включено** | Параметр **Show LinkedIn features in Office applications** (Показывать функции LinkedIn в приложениях Office) в Office 2016 включен. Пользователи в вашей организации можно использовать функции LinkedIn в приложениях Office 2016.
   **Disabled** | Параметр **Show LinkedIn features in Office applications** (Показывать функции LinkedIn в приложениях Office) в Office 2016 отключен, и пользователи не могут изменить это. Пользователи в вашей организации не могут использовать функции LinkedIn в приложениях Office 2016.

Эта групповая политика касается только приложений Office 2016 для локальных компьютеров. Если пользователи отключат LinkedIn в приложениях Office 2016, они по-прежнему могут видеть функции LinkedIn в Office 365.

## <a name="next-steps"></a>Дальнейшие действия

* [Согласие пользователя и данных с общим доступом для LinkedIn](linkedin-user-consent.md)

* [Сведения и функции LinkedIn в приложениях Майкрософт](https://go.microsoft.com/fwlink/?linkid=850740)

* [Центр справки LinkedIn](https://www.linkedin.com/help/linkedin)

* [Просмотр текущих настроек интеграции с LinkedIn на портале Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
