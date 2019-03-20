---
title: Согласие на службы LinkedIn для вашей организации — Azure Active Directory | Документация Майкрософт
description: Сведения о том, как включить или отключить интеграцию с LinkedIn для приложений Майкрософт в Azure Active Directory.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: abcb1696efe44293d01153aa37a9835ba5f43370
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199704"
---
# <a name="consent-to-linkedin-integration-for-your-azure-active-directory-organization"></a>Согласие на интеграцию с LinkedIn для вашей организации Azure Active Directory

В этой статье вы узнаете, как включить или отключить интеграцию с LinkedIn для вашей организации в центре администрирования Azure Active Directory (Azure AD).

> [!IMPORTANT]
> Настройки интеграции с LinkedIn сейчас разворачивается в Azure AD организации. Если она внедрена для вашей организации, он включается по умолчанию.
> 
> Исключения:
> * Этот параметр недоступен для клиентов, использующих Microsoft Cloud for US Government, Microsoft Cloud Germany, а также служб Azure и Office 365, обслуживаемых 21Vianet в Китае.
> * Этот параметр по умолчанию отключен для клиентов, подготовленных в Германии. Обратите внимание, что этот параметр недоступен для клиентов, использующих Microsoft Cloud Germany.
> * Этот параметр по умолчанию отключен для клиентов, подготовленных во Франции.
>
> Интеграция работает только в том случае, если он используется *и* после пользователям разрешать приложениям доступ к данным компании от своего имени. Сведения о настройке согласия пользователя, см. в разделе [об удалении доступа пользователя к приложению](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-the-azure-portal"></a>Включение и отключение интеграции с LinkedIn для ваших пользователей на портале Azure

Вы можете разрешить или запретить интеграцию с LinkedIn для всех пользователей клиента или для выбранных пользователей клиента.

1. Войдите в [Центр администрирования Azure Active Directory](https://aad.portal.azure.com/) с помощью учетной записи глобального администратора клиента Azure AD.
2. Выберите **Пользователи**.
3. В колонке **Пользователи** выберите **Параметры пользователей**.
4. В разделе **LinkedIn account connections** (Подключения к учетной записи LinkedIn).

   * Выберите **Да** предоставить согласие для всех пользователей в организации для подключения учетной записи, чтобы получить доступ к LinkedIn контакты в некоторых приложениях Майкрософт.
   * Выберите **выбранные** принять это разрешение только для выбранных пользователей в организации для подключения учетной записи, чтобы получить доступ к LinkedIn контакты в некоторых приложениях Майкрософт.
   * Выберите **нет** отказаться от получения согласия для пользователей в вашей организации для подключения учетной записи, чтобы получить доступ к LinkedIn контакты в некоторых приложениях Майкрософт.
    ![Включение интеграции с LinkedIn в организации](./media/linkedin-integration/linkedin-integration.png)
5. По завершении сохраните параметры, нажав кнопку **Сохранить**.

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-group-policy"></a>Включение и отключение интеграции с LinkedIn для ваших пользователей в групповой политике

1. Скачайте [файлы административных шаблонов Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030).
2. Извлеките файлы **ADMX** и скопируйте их в центральное хранилище.
3. Откройте средство управления групповыми политиками.
4. Создайте объект групповой политики со следующими параметрами: **Конфигурация пользователя** > **Административные шаблоны** > **Microsoft Office 2016** > **Разное** > **Показывать функции LinkedIn в приложениях Office**.
5. Выберите **Включено** или **Отключено**.
  
   Состояние | Результат
   ------ | ------
   **Включено** | Параметр **Show LinkedIn features in Office applications** (Показывать функции LinkedIn в приложениях Office) в Office 2016 включен. Пользователи в вашей организации могут использовать функции LinkedIn в приложениях Office.
   **Отключено** | Параметр **Show LinkedIn features in Office applications** (Показывать функции LinkedIn в приложениях Office) в Office 2016 отключен, и пользователи не могут изменить это. Пользователи в вашей организации не могут использовать функции LinkedIn в приложениях Office 2016.

Эта групповая политика касается только приложений Office 2016 для локальных компьютеров. Функции LinkedIn будут отображаться в карточках профиля в Office 365, даже если пользователи отключат LinkedIn в приложениях Office 2016.

## <a name="learn-more"></a>Подробнее

* [Согласие пользователя и предоставление общего доступа к данным через подключение к учетной записи LinkedIn](linkedin-user-consent.md)

* [Сведения и функции LinkedIn в приложениях Майкрософт](https://go.microsoft.com/fwlink/?linkid=850740)

* [Центр справки LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Дальнейшие действия

Просмотреть текущие настройки интеграции с LinkedIn на портале Azure можно по следующей ссылке:

[Просмотр текущих настроек интеграции с LinkedIn на портале Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
