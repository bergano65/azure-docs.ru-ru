---
title: Скачайте список пользователей на портале Azure Active Directory | Документация Майкрософт
description: Вы также скачиваете записи пользователей в центре администрирования Azure в Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 04/16/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 878f091ac6ac40f6ce434710e809c790f382c1ac
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92376447"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Скачивание списка пользователей на портале Azure Active Directory

Azure Active Directory (Azure AD) поддерживает операции многопользовательского импорта (создания).

## <a name="required-permissions"></a>Необходимые разрешения

Чтобы скачать список пользователей из центра администрирования Azure AD, необходимо войти с помощью пользователя, назначенного одной или нескольким ролям администратора на уровне Организации в Azure AD (требуется минимальная роль администратора пользователей). Гостевой участник и разработчик приложений не считаются ролями администратора.

## <a name="to-download-a-list-of-users"></a>Загрузка списка пользователей

1. [Войдите в организацию Azure AD](https://aad.portal.azure.com) с помощью учетной записи администратора пользователя в Организации.
2. Перейдите к Azure Active Directory > пользователей. Затем выберите пользователей, которых нужно включить в загрузку, загрузив поле в левом столбце рядом с каждым пользователем. Примечание. в настоящее время невозможно выбрать всех пользователей для экспорта. Каждый из них должен быть выбран отдельно.
3. В Azure AD выберите **Пользователи**  >  **скачать пользователей**.
4. На странице **скачать пользователей** выберите **начать** , чтобы получить CSV-файл со списком свойств профиля пользователя. При наличии ошибок можно загрузить и просмотреть файл результатов на странице Результаты массовой операции. Файл содержит причину каждой ошибки.

   ![Выберите, куда нужно загрузить список пользователей.](./media/users-bulk-download/bulk-download.png)

   В файле загрузки будет содержаться отфильтрованный список пользователей.

   Включаются следующие атрибуты пользователя:

   - userPrincipalName
   - displayName
   - surname
   - mail
   - givenName
   - objectId
   - userType
   - jobTitle
   - department
   - AccountEnabled
   - usageLocation
   - streetAddress
   - Состояние
   - country
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - TelephoneNumber
   - mobile
   - аусентикатионфоненумбер
   - аусентикатионалтернативефоненумбер
   - аусентикатионемаил
   - алтернатимаиладдресс
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Проверка состояния

Состояние ожидающих выполнения незавершенных запросов можно просмотреть на странице **результатов групповой операции** .

[![Проверьте состояние на странице результатов с массовыми операциями.](./media/users-bulk-download/bulk-center.png)](./media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Ограничения службы пакетного скачивания

Каждое групповое действие по созданию списка пользователей может выполняться в течение одного часа. Это позволяет создавать и скачивать список по крайней мере 500 000 пользователей.

## <a name="next-steps"></a>Дальнейшие шаги

- [Групповое добавление пользователей](users-bulk-add.md)
- [Массовое удаление пользователей](users-bulk-delete.md)
- [Массовое восстановление пользователей](users-bulk-restore.md)
