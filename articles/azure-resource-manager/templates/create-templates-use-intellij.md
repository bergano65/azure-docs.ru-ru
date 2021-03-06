---
title: Развертывание шаблона — IntelliJ IDEA
description: Сведения о создании и развертывании первого шаблона Azure Resource Manager с помощью IntelliJ IDEA.
ms.devlang: java
ms.date: 08/01/2019
ms.topic: conceptual
ms.openlocfilehash: 1046c942e6bec4ded332027b7856110238b8f2d7
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75680619"
---
# <a name="create-and-deploy-azure-resource-manager-templates-by-using-the-intellij-idea"></a>Создание и развертывание шаблонов Azure Resource Manager с помощью IntelliJ IDEA

В этой статье содержатся сведения о развертывании шаблона Resource Manager в Azure с помощью IntelliJ IDEA, а также о редактировании и обновлении шаблона непосредственно из IDE. Шаблоны Resource Manager — это JSON-файлы, которые определяют ресурсы, необходимые для развертывания решения. Основные понятия, связанные с развертыванием решений Azure и управлением ими, см. в разделе [Общие сведения о развертывании шаблонов](overview.md).

![Схема портала шаблона диспетчер ресурсов](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Изучив это руководство, вы развернете учетную запись службы хранилища Azure. Тем же способом развертывайте ресурсы Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Технические условия

Для работы с этой статьей необходимо иметь следующее.

* Установленный [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition или Community Edition.
* См. подробнее об [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) в руководстве по [управлению подключаемыми модулями IntelliJ](https://www.jetbrains.com/help/idea/managing-plugins.html).
* [Войдите](/java/azure/intellij/azure-toolkit-for-intellij-sign-in-instructions) в свою учетную запись Azure для работы с Azure Toolkit for IntelliJ.

## <a name="deploy-a-quickstart-template"></a>Развертывание шаблона быстрого запуска

Чтобы не создавать шаблон с нуля, откройте его в [Шаблонах быстрого запуска Azure](https://azure.microsoft.com/resources/templates/). Шаблоны быстрого запуска Azure — это репозиторий для шаблонов Resource Manager. Шаблон, используемый в этой статье, называется [Создание стандартной учетной записи хранения](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/). Он определяет ресурс учетной записи службы хранилища Azure.

1. Щелкните правой кнопкой мыши и сохраните [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) и [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) на локальном компьютере.

1. Если ваш набор Azure Toolkit правильно установлен и в него выполнен вход, на боковой панели IntelliJ IDEA отобразится Azure Explorer. Щелкните правой кнопкой мыши **Управление ресурсами** и выберите **Создать развертывание**.

    ![Шаблон Resource Manager: щелчок правой кнопкой мыши для создания развертывания](./media/create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Укажите **имя развертывания**, **подписку**, **группу ресурсов** и **регион**. В рамках этого руководства мы развертываем шаблон в новой группе ресурсов `testRG`. Затем в качестве пути для **шаблона ресурса** укажите `azuredeploy.json`, а для **параметров ресурса** — скачанный файл `azuredeploy.parameters.json`.

    ![Шаблон Resource Manager: выбор файлов для создания развертывания](./media/create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Нажмите кнопку "Resource Parameters", и развертывание начнется. Ожидая завершения развертывания, вы можете просматривать ход выполнения в **строке состояния** IntelliJ IDEA внизу страницы.

    ![Шаблон Resource Manager: состояние развертывания](./media/create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Просмотр имеющегося развертывания

1. После завершения развертывания отобразится новая группа ресурсов `testRG` и созданное развертывание. Щелкните развертывание правой кнопкой мыши, и отобразится список возможных действий. Выберите **Показать свойства**.

    ![Просмотр развертывания шаблона Resource Manager](./media/create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Откроется представление вкладки, отображающее некоторые полезные свойства, такие как состояние развертывания и структура шаблона.

    ![Шаблон Resource Manager: отображение свойств развертывания](./media/create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Редактирование и обновление имеющегося развертывания

1. Выберите **Edit Deployment** (Изменить развертывание) в контекстном меню или в представлении отображения свойств. Откроется еще одно представление вкладки с файлом шаблона и файлами параметров для развертывания в Azure. Чтобы сохранить эти файлы на локальном компьютере, щелкните **Export Template File** (Экспортировать файл шаблона) или **Export Parameter Files** (Экспортировать файлы параметров).

    ![Шаблон Resource Manager: редактирование развертывания](./media/create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Вы можете отредактировать два файла на этой странице и развернуть изменения в Azure. Мы изменяем значение **storageAccountType** в файлах параметров с `Standard_LRS` на `Standard_GRS`. Затем щелкните **Обновить развертывание** внизу страницы и подтвердите обновление.

    ![Шаблон Resource Manager: редактирование развертывания](./media/create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. После того как вы обновите развертывание на портале, созданная учетная запись хранения должна отображаться как `Standard_GRS`.

## <a name="clean-up-resources"></a>Очистка ресурсов

1. Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов. Это можно сделать на портале Azure или с помощью Azure CLI. В Azure Explorer в IntelliJ IDEA щелкните правой кнопкой мыши созданную **группу ресурсов** и выберите "Удалить".

    ![Удаление группы ресурсов в Azure Explorer из IntelliJ IDEA](./media/create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Обратите внимание, что удаление развертывания никоим образом не повлияет на ресурсы, созданные при развертывании. Если соответствующая группа ресурсов или определенные ресурсы вам больше не нужны, удалите их.

## <a name="next-steps"></a>Дальнейшие действия

Основная задача этой статьи — использовать IntelliJную идею для развертывания существующего шаблона из шаблонов быстрого запуска Azure. Кроме того, в нем содержатся сведения о просмотре и обновлении имеющегося развертывания в Azure. Шаблоны быстрого запуска Azure могут не обеспечить вас всем необходимым. Дополнительные сведения о разработке шаблонов см. в новой серии

> [!div class="nextstepaction"]
> [руководств для начинающих](./template-tutorial-create-first-template.md).

> [!div class="nextstepaction"]
> [Azure для разработчиков облачных решений Java](https://docs.microsoft.com/azure/java)
