---
title: Загрузите список пользователей (предварительный просмотр) на портале Active Directory Azure (фото) Документы Майкрософт
description: Загружайте записи пользователей оптом в центре управления Azure в Active Directory Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 02/06/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4716ff9547f64dc6551b4d4adb0a8578da9fa83e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063838"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>Загрузите список пользователей (предварительный просмотр) на портале Active Directory Azure

Активный каталог Azure (Azure AD) поддерживает операции по импорту (созданию) навалом пользователей.

## <a name="required-permissions"></a>Необходимые разрешения

Чтобы загрузить список пользователей из центра ad Azure, необходимо подписаться на пользователя, назначенного на одну или несколько ролей администратора организации в Azure AD. Приглашенный и разработчик приложений не считаются ролями администратора.

## <a name="to-download-a-list-of-users"></a>Загрузить список пользователей

1. [Восвай в организацию Azure AD](https://aad.portal.azure.com) с учетной записью администратора пользователя в организации.
2. Перейдите в Active Directory Azure > пользователей. Затем выберите пользователей, которых вы хотите включить в загрузку, пометив поле в левой колонке рядом с каждым пользователем. Примечание: В настоящее время нет возможности выбрать всех пользователей на экспорт. Каждый из них должен быть индивидуально выбран.
3. В Azure AD выберите **пользователей** > **Скачать пользователей**.
4. На странице **пользователей Download** выберите **Начало** получения файла CSV, в которой перечислены свойства профиля пользователя. Если есть ошибки, вы можете загрузить и просмотреть файл результатов на странице результатов операции Bulk. Файл содержит причину каждой ошибки.

   ![Выберите, где вы хотите список пользователей, которых вы хотите загрузить](./media/users-bulk-download/bulk-download.png)

   Файл загрузки будет содержать отфильтрованный список пользователей.

   Приведены следующие атрибуты пользователя:

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
   - state
   - country
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - TelephoneNumber
   - mobile
   - аутентификацияPhoneNumber
   - аутентификацияAlternativePhoneНомер
   - аутентификацияEmail
   - alternateEmailАдрес
   - Возрастная группа
   - согласиеПредоставленоДляимый
   - legalAgeGroupКлассификация

## <a name="check-status"></a>Проверка состояния

Вы можете увидеть состояние ожидающих навалочных запросов на странице **результаты (предварительный просмотр) результатов операции Bulk.**

   ![Проверка состояния загрузки на странице Результаты массовых операций](./media/users-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Ограничения обслуживания массовых скачиваний

Каждое массовое действие для создания списка пользователей может работать до одного часа. Это позволяет создать и загрузить список не менее 500 000 пользователей.

## <a name="next-steps"></a>Дальнейшие действия

- [Массовое добавление пользователей](users-bulk-add.md)
- [Массовое удаление пользователей](users-bulk-delete.md)
- [Массовые восстановления пользователей](users-bulk-restore.md)
