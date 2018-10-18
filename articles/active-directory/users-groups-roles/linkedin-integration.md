---
title: Включение интеграции подключений LinkedIn в Azure Active Directory | Документация Майкрософт
description: Сведения о том, как включить или отключить подключения к учетным записям LinkedIn для приложений Майкрософт в Azure Active Directory.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/11/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 0eaa2656313ecd9b64503051265dc16285f0a1f3
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44492876"
---
# <a name="linkedin-account-connections"></a>Подключения к учетной записи LinkedIn

В статье рассматриваются вопросы включения или отключения подключений к учетным записям LinkedIn для вашего клиента в центре администрирования Azure Active Directory (Azure AD).

> [!IMPORTANT]
> Сейчас для клиентов Azure AD происходит внедрение параметра подключения к учетным записям LinkedIn. Если она внедрена для вашего клиента, она включена по умолчанию. 
> 
> Исключения:
> * Этот параметр недоступен для клиентов, использующих Microsoft Cloud for US Government, Microsoft Cloud Germany, а также служб Azure и Office 365, обслуживаемых 21Vianet в Китае.
> * Этот параметр по умолчанию отключен для клиентов, подготовленных в Германии. Обратите внимание, что этот параметр недоступен для клиентов, использующих Microsoft Cloud Germany.
> * Этот параметр по умолчанию отключен для клиентов, подготовленных во Франции.

> Интеграция работает, только если она включена *и* вы разрешили пользователям устанавливать приложения, обращающимся к данным компании от своего имени. Сведения о настройке разрешения см. в статье [Удаление доступа пользователя к приложению](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-or-disable-linkedin-account-connections-for-your-tenant-in-the-azure-portal"></a>Включение или отключение подключений к учетной записи LinkedIn для пользователей клиента на портале Azure

Вы можете разрешить или запретить подключения к учетным записям LinkedIn для всех пользователей клиента или для ограниченной группы.

1. Войдите в [Центр администрирования Azure Active Directory](https://aad.portal.azure.com/) с помощью учетной записи глобального администратора клиента Azure AD.
2. Выберите **Пользователи**.
3. В колонке **Пользователи** выберите **Параметры пользователей**.
4. В разделе **LinkedIn account connections** (Подключения к учетной записи LinkedIn).
  * Выберите **Да**, чтобы включить подключения к учетной записи LinkedIn для всех пользователей вашего клиента.
  * Выберите **Избранные**, чтобы включить подключения к учетной записи LinkedIn лишь для ограниченной группы пользователей вашего клиента.
  * Выберите **Нет**, чтобы отключить подключения к учетной записи LinkedIn для всех пользователей в разделе ![LinkedIn account connections](./media/linkedin-integration/linkedin-integration.png) (Подключения к учетной записи LinkedIn).
5. По завершении сохраните параметры, нажав кнопку **Сохранить**.

## <a name="enable-or-disable-linkedin-account-connections-for-your-tenant-using-group-policy"></a>Включение или отключение подключений к учетной записи LinkedIn для пользователей клиента с помощью групповой политики

1. Скачайте [файлы административных шаблонов Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030).
2. Извлеките файлы **ADMX** и скопируйте их в центральное хранилище.
3. Откройте средство управления групповыми политиками.
4. Создайте объект групповой политики со следующими параметрами: **Конфигурация пользователя** > **Административные шаблоны** > **Microsoft Office 2016** > **Разное** > **Show LinkedIn features in Office applications** (Показывать функции LinkedIn в приложениях Office).
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

## <a name="next-steps"></a>Дополнительная информация
Просмотреть текущие настройки подключений к учетной записи LinkedIn на портале Azure можно по следующей ссылке.

[Настройка подключений к учетной записи LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 