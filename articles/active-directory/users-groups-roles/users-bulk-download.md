---
title: Скачайте список пользователей (Предварительная версия) на портале Azure Active Directory | Документация Майкрософт
description: Вы также скачиваете записи пользователей в центре администрирования Azure в Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 07/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: c59a0b40065be1554e79186227cd2e90d68e7222
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174195"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>Скачайте список пользователей (Предварительная версия) на портале Azure Active Directory

Azure Active Directory (Azure AD) поддерживает операции многопользовательского импорта (создания).

## <a name="required-permissions"></a>Необходимые разрешения

Чтобы скачать список пользователей из центра администрирования Azure AD, необходимо войти в учетную запись с помощью пользователя, назначенного одной или нескольким ролям администратора уровня Организации в Azure AD. Гостевой участник и разработчик приложений не считаются ролями администратора.

## <a name="to-download-a-list-of-users"></a>Загрузка списка пользователей

1. [Войдите в организацию Azure AD](https://aad.portal.azure.com) с помощью учетной записи администратора пользователя в Организации.
1. В Azure AD выберите **пользователи** > **скачать пользователей**.
1. На странице **скачать пользователей** выберите **начать** , чтобы получить CSV-файл со списком свойств профиля пользователя. При наличии ошибок можно загрузить и просмотреть файл результатов на странице результатов групповой операции. Файл содержит причину каждой ошибки.

   ![Выберите, куда нужно загрузить список пользователей.](./media/users-bulk-download/bulk-download.png)

## <a name="check-status"></a>Проверка состояния

Состояние ожидающих выполнения незавершенных запросов можно просмотреть на странице **Результаты групповой операции (Предварительная версия)** .

   ![Проверка состояния отправки на странице результатов с массовыми операциями](./media/users-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Ограничения службы пакетного скачивания

Каждое групповое действие по созданию списка пользователей может выполняться в течение одного часа. Это позволяет создавать и скачивать список по крайней мере 500 000 пользователей.

## <a name="next-steps"></a>Следующие шаги

- [Групповое добавление пользователей](users-bulk-add.md)
- [Групповое удаление пользователей](users-bulk-delete.md)
- [Пользователи с массовым восстановлением](users-bulk-restore.md)
