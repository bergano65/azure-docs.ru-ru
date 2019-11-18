---
title: Управление ресурсами — портал Azure
description: Используйте портал Azure и Azure Resource Manager для управления ресурсами. Показывает, как развертывать и удалять ресурсы.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 5c0e09930ee53733b36e987356cef2963416006f
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149824"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Управление ресурсами Azure с помощью портал Azure

Узнайте, как использовать [портал Azure](https://portal.azure.com) с [Azure Resource Manager](resource-group-overview.md) для управления ресурсами Azure. Сведения об управлении группами ресурсов см. [в статье Управление группами ресурсов Azure с помощью портал Azure](./manage-resource-groups-portal.md).

Другие статьи об управлении ресурсами:

- [Управление ресурсами Azure с помощью Azure CLI](./manage-resources-cli.md)
- [Управление ресурсами Azure с помощью Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Развертывание ресурсов в группе ресурсов

После создания шаблона диспетчер ресурсов можно использовать портал Azure для развертывания ресурсов Azure. Сведения о создании шаблона см. [в разделе Краткое руководство. Создание и развертывание шаблонов Azure Resource Manager с помощью портал Azure](./resource-manager-quickstart-create-templates-use-the-portal.md). Сведения о развертывании шаблона с помощью портала см. в разделе [развертывание ресурсов с использованием шаблонов диспетчер ресурсов и портал Azure](resource-group-template-deploy-portal.md).

## <a name="open-resources"></a>Открытие ресурсов

Ресурсы Azure упорядочены по службам Azure и группам ресурсов. В следующих процедурах показано, как открыть учетную запись хранения с именем **mystorage0207**. Виртуальная машина находится в группе ресурсов с именем **mystorage0207rg**.

Чтобы открыть ресурс по типу службы, выполните следующие действия.

1. Войдите на [портале Azure](https://portal.azure.com).
2. В левой области выберите службу Azure. В этом случае это **учетные записи хранения**.  Если служба не отображается в списке, выберите **все службы**, а затем выберите тип службы.

    ![Открытие ресурса Azure на портале](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Выберите ресурс, который необходимо открыть.

    ![Открытие ресурса Azure на портале](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Учетная запись хранения выглядит следующим образом:

    ![Открытие ресурса Azure на портале](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Чтобы открыть ресурс по группе ресурсов, выполните следующие действия.

1. Войдите на [портале Azure](https://portal.azure.com).
2. В области слева выберите **группы ресурсов** , чтобы получить список ресурсов в группе.
3. Выберите ресурс, который необходимо открыть. 

## <a name="manage-resources"></a>Управление ресурсами

На портале для конкретного ресурса можно просмотреть параметры управления.

![Управление ресурсами Azure](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

На снимке экрана показаны параметры управления для виртуальной машины Azure. Вы можете выполнять такие операции, как запуск, перезапуск и остановка виртуальной машины.

## <a name="delete-resources"></a>Удаление ресурсов

1. Откройте ресурс на портале. Инструкции см. в разделе [Открытие ресурсов](#open-resources).
2. Нажмите кнопку **Удалить**. На следующем снимке экрана показаны параметры управления для виртуальной машины.

    ![Удаление ресурса Azure](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Введите имя ресурса, чтобы подтвердить удаление, а затем выберите **Удалить**.

Дополнительные сведения о том, как Azure Resource Manager упорядочивают удаление ресурсов, см. в разделе [Azure Resource Manager удаление группы ресурсов](./resource-group-delete.md).

## <a name="move-resources"></a>Перемещение ресурсов

1. Откройте ресурс на портале. Инструкции см. в разделе [Открытие ресурсов](#open-resources).
2. Выберите **Переместить**. На следующем снимке экрана показаны параметры управления для учетной записи хранения.

    ![перемещение ресурса Azure](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Выберите **переместить в другую группу ресурсов** или **Перейти к другой подписке** в зависимости от ваших потребностей.

Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).

## <a name="lock-resources"></a>Блокировка ресурсов

Блокировка запрещает другим пользователям в организации случайно удалить или изменить критически важные ресурсы, такие как подписка Azure, Группа ресурсов или ресурс. 

1. Откройте ресурс на портале. Инструкции см. в разделе [Открытие ресурсов](#open-resources).
2. Выберите **блокировки**. На следующем снимке экрана показаны параметры управления для учетной записи хранения.

    ![Блокировка ресурса Azure](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Выберите **Добавить**, а затем укажите свойства блокировки.

Дополнительные сведения см. в статье [Блокировка ресурсов с помощью диспетчера ресурсов Azure](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Добавление тегов к ресурсам

Теги позволяют логически упорядочивать группы ресурсов и ресурсы. 

1. Откройте ресурс на портале. Инструкции см. в разделе [Открытие ресурсов](#open-resources).
2. Нажмите **Теги**. На следующем снимке экрана показаны параметры управления для учетной записи хранения.

    ![Тег ресурса Azure](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Укажите свойства тега и нажмите кнопку **сохранить**.

Дополнительные сведения см. [в статье Использование тегов для Организации ресурсов Azure](./resource-group-using-tags.md#portal).

## <a name="monitor-resources"></a>Мониторинг ресурсов

При открытии ресурса на портале представлены диаграммы и таблицы по умолчанию для мониторинга этого типа ресурсов. На следующем снимке экрана показаны графы для виртуальной машины.

![мониторинг ресурса Azure](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Вы можете выбрать значок закрепления в правом верхнем углу графики, чтобы закрепить граф на панели мониторинга. Чтобы узнать о том, как работать с панелями мониторинга, ознакомьтесь с разделом [Создание панелей мониторинга и предоставление общего доступа к ним на портале Azure](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Управление доступом к ресурсам

[Управление доступом на основе ролей (RBAC)](../role-based-access-control/overview.md) — это способ управления доступом к ресурсам в Azure. Дополнительные сведения см. в статье [Управление доступом с помощью RBAC и портала Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Дополнительная информация

- Сведения о Azure Resource Manager см. в разделе [Общие сведения о Azure Resource Manager](./resource-group-overview.md).
- Сведения о синтаксисе шаблона диспетчер ресурсов см. в разделе [Общие сведения о структуре и синтаксисе шаблонов Azure Resource Manager](./resource-group-authoring-templates.md).
- Дополнительные сведения о разработке шаблонов см. в пошаговых [руководствах](/azure/azure-resource-manager/).
- Сведения о том, как просмотреть схемы шаблонов Azure Resource Manager, см. в разделе [Справочник по шаблонам](/azure/templates/).
