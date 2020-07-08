---
title: Экспорт или удаление параметров портал Azure
description: Узнайте, как можно экспортировать или удалять пользовательские параметры, частные панели мониторинга и пользовательские параметры в портал Azure.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: how-to
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: 248c643c5f1865451abbe201e7033efedb8a7e39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763947"
---
# <a name="export-or-delete-user-settings"></a>Экспорт или удаление параметров пользователя

Для создания пользовательского интерфейса можно использовать параметры и функции в портал Azure. Сведения о пользовательских параметрах хранятся в Azure. Вы можете экспортировать или удалить следующие пользовательские данные:

* Частные панели мониторинга в портал Azure
* Пользовательские параметры, такие как избранные подписки или каталоги, а также каталог последнего входа
* Темы и другие параметры пользовательского портала

Рекомендуется экспортировать и проверить параметры перед их удалением. Перестроение панелей мониторинга или повторное выполнение пользовательских параметров может занять много времени.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Экспорт или удаление параметров портала

1. Войдите на [портал Azure](https://portal.azure.com).

1. В заголовке портала выберите ![ Параметры значок Параметры ](media/azure-portal-export-delete-settings/settings-icon.png) **Settings**.

1. Выберите **Export all settings** (Экспортировать все параметры) или **Delete all settings and private dashboards** (Удалить все параметры и частные панели мониторинга).

    ![Параметры и параметры портал Azure](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      Эти действия описаны в следующей таблице.

      | Действие | Описание |
      | --- | --- |
      | **Export all settings** (Экспортировать все параметры) | Создает *JSON* -файл, содержащий пользовательские параметры, такие как цветовая тема, избранное и частные панели мониторинга.|
      | **Delete all settings and private dashboards** (Удалить все параметры и частные панели мониторинга) | Удаляет все ссылки на частные панели мониторинга и другие настраиваемые параметры, сделанные на портале. |

> [!NOTE]
> Из-за динамического характера пользовательских параметров и риска повреждения данных нельзя импортировать параметры из *JSON* -файла.
>
>

## <a name="next-steps"></a>Дальнейшие шаги

* [Предоставление общего доступа к панелям мониторинга Azure с помощью управления доступом на основе ролей](azure-portal-dashboard-share-access.md)
* [Добавление, удаление и переупорядочение избранного](azure-portal-add-remove-sort-favorites.md)
