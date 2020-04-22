---
title: Создание и администрирование проектов Миграции Azure
description: Поиск, создание, управление и удаление проектов в Azure Migrate.
ms.topic: how-to
ms.date: 04/19/2020
ms.openlocfilehash: f5079ed979d98f2c6f0c654c860c6f176f366497
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676397"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Создание и администрирование проектов Миграции Azure

В этой статье описывается, как создавать, управлять и удалять проекты [Azure Migrate.](migrate-services-overview.md)


## <a name="create-a-project-for-the-first-time"></a>Создание проекта впервые

При первом настройке Azure Migrate создается проект и добавляется инструмент оценки или миграции. [Следуйте этим инструкциям,](how-to-add-tool-first-time.md) чтобы настроить в первый раз.

## <a name="create-additional-projects"></a>Создание дополнительных проектов

Если у вас уже есть проект Azure Migrate и вы хотите создать дополнительный проект, сделайте следующее:  

1. На [общедоступном портале Azure](https://portal.azure.com) или [в правительстве Azure](https://portal.azure.us)ищите **Azure Migrate.**
2. На панели мониторинга Azure Migrate > **серверов**выберите **изменение** в правом верхнем углу.

   ![Изменение проекта Azure Migrate](./media/create-manage-projects/switch-project.png)

3. Чтобы создать новый проект, **выберите нажмите здесь**.

   ![Создание второго проекта Azure Migrate](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>Найти проект

Найти проект следующим образом:

1. На [портале Azure](https://portal.azure.com)ищите **Azure Migrate**.
2. В панели мониторинга Azure Migrate > **серверов**выберите **изменение** в правом верхнем углу.

    ![Переключиться на существующий проект Azure Migrate](./media/create-manage-projects/switch-project.png)

3. Выберите подходящий проект подписки и Azure Migrate.


Если вы создали проект в [предыдущей версии](migrate-services-overview.md#azure-migrate-versions) Azure Migrate, найдите его следующим образом:

1. На [портале Azure](https://portal.azure.com)ищите **Azure Migrate**.
2. В панели мониторинга Azure Migrate, если вы создали проект в предыдущей версии, появляется баннер, ссылающийся на старые проекты. Выберите баннер.

    ![Доступ к существующим проектам](./media/create-manage-projects/access-existing-projects.png)

3. Просмотрите список старых проектов.


## <a name="delete-a-project"></a>Удаление проекта

Удалить следующим образом:

1. Откройте группу ресурсов Azure, в которой был создан проект.
2. На странице группы ресурсов выберите **«Показать скрытые типы».**
3. Выберите проект мигрировать, который необходимо удалить, и связанные с ним ресурсы.
    - Тип ресурса **— Microsoft.Migrate/migrateprojects**.
    - Если группа ресурсов используется исключительно в проекте Azure Migrate, можно удалить всю группу ресурсов.


Обратите внимание на следующее.

- При удалении удаляются как проект, так и метаданные об обнаруженных машинах.
- Если вы используете старую версию Azure Migrate, откройте группу ресурсов Azure, в которой был создан проект. Выберите проект мигрировать, который необходимо удалить (тип ресурса — **проект «Миграция»).**
- Если вы используете анализ зависимостей с рабочим пространством Analytics журналов Azure:
    - Если вы прикрепили рабочее пространство Log Analytics к инструменту оценки сервера, рабочее пространство не удаляется автоматически. Одно и то же рабочее пространство Log Analytics можно использовать для нескольких сценариев.
    - Если вы хотите удалить рабочее пространство Log Analytics, сделайте это вручную.

### <a name="delete-a-workspace-manually"></a>Удаление рабочего пространства вручную

1. Перейдите к рабочей области Log Analytics, связанной с проектом.

    - Если вы не удалили проект Azure Migrate, вы можете найти ссылку на рабочее пространство в **оценке сервера Essentials.** > **Server Assessment**
       ![LA Рабочее пространство](./media/create-manage-projects/loganalytics-workspace.png).
       
    - Если вы уже удалили проект Azure Migrate, выберите **группы ресурсов** в левом стеле портала Azure и найдите рабочее пространство.
       
2. [Следуйте инструкциям](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) по удалению рабочего пространства.

## <a name="next-steps"></a>Следующие шаги

Добавление инструментов [оценки](how-to-assess.md) или [миграции](how-to-migrate.md) в проекты Azure Migrate.
