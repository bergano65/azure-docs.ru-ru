---
title: Экспорт или удаление настроек портала Azure
description: Узнайте, как можно экспортировать или удалять настройки пользователя, частные панели мониторинга и пользовательские настройки на портале Azure.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: accdfbd939fad73ca7d008450c358d366e7f8b70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900760"
---
# <a name="export-or-delete-user-settings"></a>Экспорт или удаление параметров пользователя

Настройки и функции на портале Azure можно использовать для создания пользовательского интерфейса. Информация о настройках пользовательских данных хранится в Azure. Вы можете экспортировать или удалять следующие данные пользователя:

* Частные панели мониторинга на портале Azure
* Настройки пользователя, такие как любимые подписки или каталоги, и последний каталог входа в систему
* Темы и другие настройки пользовательских порталов

Рекомендуется экспортировать и просматривать настройки, прежде чем удалять их. Восстановление панелей мониторинга или повторное выполнение пользовательских настроек может занять много времени.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Экспорт или удаление параметров портала

1. Войдите на [портал Azure](https://portal.azure.com).

1. В заголовке портала выберите ![](media/azure-portal-export-delete-settings/settings-icon.png) **настройки**настроек.

1. Выберите **Export all settings** (Экспортировать все параметры) или **Delete all settings and private dashboards** (Удалить все параметры и частные панели мониторинга).

    ![Параметры настроек и настроек портала Azure](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      В следующей таблице описаны эти действия.

      | Действие | Описание |
      | --- | --- |
      | **Export all settings** (Экспортировать все параметры) | Создает файл *.json,* содержащий настройки пользователя, такие как цветовая тема, избранное и частные панели мониторинга.|
      | **Delete all settings and private dashboards** (Удалить все параметры и частные панели мониторинга) | Удаляет все ссылки на частные панели мониторинга и другие пользовательские настройки, которые вы сделали на портале. |

> [!NOTE]
> Из-за динамического характера настроек пользователя и риска повреждения данных нельзя импортировать настройки из файла *.json.*
>
>

## <a name="next-steps"></a>Дальнейшие действия

* [Предоставление общего доступа к панелям мониторинга Azure с помощью управления доступом на основе ролей](azure-portal-dashboard-share-access.md)
* [Добавить, удалить и изменить избранное](azure-portal-add-remove-sort-favorites.md)
