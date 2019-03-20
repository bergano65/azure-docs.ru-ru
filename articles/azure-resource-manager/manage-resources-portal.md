---
title: Управлять ресурсами Azure с помощью портала Azure | Документация Майкрософт
description: Используйте портал Azure и Azure Resource Manager для управления ресурсами.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 20bf38b87ce29f8506a5611ecd25cf38f6d4ed61
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56824989"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Управлять ресурсами Azure с помощью портала Azure

Сведения об использовании [портала Azure](https://portal.azure.com) с [Azure Resource Manager](resource-group-overview.md) для управления ресурсами Azure. Для управления группами ресурсов, см. в разделе [групп ресурсов Azure, управлять ими с помощью портала Azure](./manage-resource-groups-portal.md).

Другие статьи об управлении ресурсами:

- [Управлять ресурсами Azure с помощью Azure CLI](./manage-resources-cli.md)
- [Управлять ресурсами Azure с помощью Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Развертывание ресурсов в группе ресурсов

После создания шаблона Resource Manager, можно использовать портал Azure для развертывания ресурсов Azure. Создание шаблона, см. в разделе [краткое руководство: по созданию и развертыванию шаблонов Azure Resource Manager с помощью портала Azure](./resource-manager-quickstart-create-templates-use-the-portal.md). Развертывание шаблона с помощью портала, см. в разделе [развертывание ресурсов с использованием шаблонов Resource Manager и портала Azure](resource-group-template-deploy-portal.md).

## <a name="open-resources"></a>Открытые ресурсы

Ресурсы Azure организованы службами Azure и групп ресурсов. Следующая процедура показывает, как открыть учетную запись хранения с именем **mystorage0207**. Виртуальная машина находится в группу ресурсов с именем **mystorage0207rg**.

Открытие ресурса типом службы:

1. Войдите на [портале Azure](https://portal.azure.com).
2. В области слева выберите службу Azure. В этом случае **учетные записи хранения**.  Если вы не видите список служб, выберите **все службы**, а затем выберите тип службы.

    ![Откройте ресурс azure на портале](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Выберите ресурс, который требуется открыть.

    ![Откройте ресурс azure на портале](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Учетная запись хранения выглядит так:

    ![Откройте ресурс azure на портале](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Чтобы открыть группу ресурсов, ресурсов:

1. Войдите на [портале Azure](https://portal.azure.com).
2. В левой области выберите **групп ресурсов** Чтобы получить список ресурсов в группе.
3. Выберите ресурс, который требуется открыть. 

## <a name="manage-resources"></a>Управление ресурсами

На портале для конкретного ресурса можно просмотреть параметры управления.

![Управление ресурсами Azure](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

На снимке экрана показаны параметры управления для виртуальной машины Azure. Можно выполнять операции, такие как запуск, перезапуск и остановка виртуальной машины.

## <a name="delete-resources"></a>Удаление ресурсов.

1. Откройте ресурс на портале. Действия, см. в разделе [открывать ресурсы](#open-resources).
2. Нажмите кнопку **Удалить**. На следующем рисунке показан параметры управления для виртуальной машины.

    ![Удаление ресурсов azure](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Введите имя ресурса, чтобы подтвердить удаление, а затем выберите **удалить**.

Дополнительные сведения о как упорядочивает удаление ресурсов в Azure Resource Manager, см. в разделе [удаление группы ресурсов Azure Resource Manager](./resource-group-delete.md).

## <a name="move-resources"></a>Перемещение ресурсов

1. Откройте ресурс на портале. Действия, см. в разделе [открывать ресурсы](#open-resources).
2. Выберите **Переместить**. На следующем рисунке показан параметры управления для учетной записи хранения.

    ![Перемещение ресурсов azure](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Выберите **переместить в другую группу ресурсов** или **Moeve в другую подписку** зависит от ваших потребностей.

Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).

## <a name="lock-resources"></a>Блокировка ресурсов

Блокировка запрещает другим пользователям в организации случайно удалить или изменить критически важные ресурсы, такие как подписки Azure, группу ресурсов или ресурс. 

1. Откройте ресурс на портале. Действия, см. в разделе [открывать ресурсы](#open-resources).
2. Выберите **блокирует**. На следующем рисунке показан параметры управления для учетной записи хранения.

    ![ресурс блокировки в azure](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Выберите **добавить**, а затем укажите свойства блокировки.

Дополнительные сведения см. в статье [Блокировка ресурсов с помощью диспетчера ресурсов Azure](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Добавление тегов к ресурсам

Добавление тегов позволяет логически Упорядочить группу ресурсов и ресурсы. 

1. Откройте ресурс на портале. Действия, см. в разделе [открывать ресурсы](#open-resources).
2. Выберите **теги**. На следующем рисунке показан параметры управления для учетной записи хранения.

    ![тег azure ресурсов](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Укажите свойства тега, а затем выберите **Сохранить**.

Сведения см. в разделе [использование тегов для организации ресурсов в Azure](./resource-group-using-tags.md#portal).

## <a name="monitor-resources"></a>Мониторинг ресурсов

Когда вы откроете ресурс, появится по умолчанию диаграммы и таблицы по отслеживать этот тип ресурса. На следующем рисунке показаны графики для виртуальной машины:

![ресурсов azure Monitor](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Можно выбрать значок булавки в правом верхнем углу диаграммы, чтобы закрепить диаграммы на панели мониторинга. Чтобы узнать о том, как работать с панелями мониторинга, ознакомьтесь с разделом [Создание панелей мониторинга и предоставление общего доступа к ним на портале Azure](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Управление доступом к ресурсам

[Управление доступом на основе ролей (RBAC)](../role-based-access-control/overview.md) — это способ управления доступом к ресурсам в Azure. Дополнительные сведения см. в статье [Управление доступом с помощью RBAC и портала Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать, Azure Resource Manager, см. в разделе [обзоре Azure Resource Manager](./resource-group-overview.md).
- Чтобы узнать синтаксис шаблона Resource Manager, см. в разделе [описание структуры и синтаксиса шаблонов Azure Resource Manager](./resource-group-authoring-templates.md).
- Чтобы научиться разрабатывать шаблоны, см. в разделе [пошаговые учебники](/azure/azure-resource-manager/).
- Чтобы просмотреть схемы шаблонов Azure Resource Manager, см. в разделе [Справочник по шаблонам](/azure/templates/).