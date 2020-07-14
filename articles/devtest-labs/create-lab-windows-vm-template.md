---
title: Создание лаборатории с помощью Azure DevTest Labs и шаблона Resource Manager
description: В рамках этого руководства вы создадите лабораторию в Azure DevTest Labs с помощью шаблона Azure Resource Manager. Администратор лаборатории настраивает лабораторию, в которой он создает виртуальные машины и настраивает политики.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: 22ba4ffeec22a45c07b096b0a754d08c8230dd8f
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85476212"
---
# <a name="tutorial-set-up-a-lab-by-using-azure-devtest-labs-resource-manager-template"></a>Руководство по Настройка лаборатории с помощью Azure DevTest Labs (на основе шаблона Resource Manager)
В рамках этого руководства вы создадите лабораторию с виртуальной машиной Windows Server 2019 Datacenter на основе шаблона Azure Resource Manager. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Вот какие действия выполняются в этом руководстве:

> [!div class="checklist"]
> * Изучение шаблона 
> * Развертывание шаблона
> * Проверка шаблона
> * Очистка ресурсов

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Нет.

## <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-dtl-create-lab-windows-vm/).

:::code language="json" source="~/quickstart-templates/101-dtl-create-lab-windows-vm/azuredeploy.json" range="1-97" highlight="51-85":::

В шаблоне определены следующие ресурсы:

- [**Microsoft.DevTestLab/labs**](/azure/templates/microsoft.devtestlab/labs);
- [**Microsoft.DevTestLab labs/virtualnetworks**](/azure/templates/microsoft.devtestlab/labs/virtualnetworks);
- [**Microsoft.DevTestLab labs/virtualmachines**](/azure/templates/microsoft.devtestlab/labs/virtualmachines).

См. [примеры шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab).

## <a name="deploy-the-template"></a>Развертывание шаблона
Чтобы выполнить развертывание автоматически, нажмите кнопку ниже. 

[![Развертывание в Azure](./media/create-lab-windows-vm-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

1. Создайте **новую группу ресурсов**, чтобы ее можно было легко удалить позднее.
1. Выберите **расположение** группы ресурсов. 
1. Введите **имя для лаборатории**. 
1. Введите **имя для виртуальной машины**. 
1. Введите **имя пользователя**, который получит доступ к виртуальной машине. 
1. Введите **пароль** для этого пользователя. 
1. Выберите **Я принимаю указанные выше условия**. 
1. Нажмите кнопку **Приобрести**.

    :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="Страница развертывания шаблона":::

## <a name="verify-the-deployment"></a>Проверка развертывания
1. Выберите элемент **Уведомления** в верхней части экрана, чтобы просмотреть состояние развертывания, и щелкните ссылку **Развертывание выполняется**.

    :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="Развертывание уведомления":::
2. На странице **Развертывание — обзор** дождитесь завершения развертывания. Выполнение этой операции (особенно при создании виртуальной машины) может потребовать времени. Затем выберите действие **Перейти к группе ресурсов** или щелкните **имя группы ресурсов**, как показано на следующем рисунке: 

    :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="Переход к группе ресурсов":::
3. На странице **Группа ресурсов** отобразится список ресурсов в этой группе ресурсов. Убедитесь, что в числе ресурсов отображается лаборатория с типом `DevTest Lab`. Кроме того, вы увидите в группе ресурсов зависимые ресурсы, например виртуальную сеть и виртуальную машину. 

    :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-home-page.png" alt-text="Домашняя страница группы ресурсов":::
4. Выберите нужную лабораторию из списка ресурсов, чтобы открыть домашнюю страницу этой лаборатории. Убедитесь, что в списке **Мои виртуальные машины** отображается виртуальная машина Windows Server 2019 Datacenter. На приведенном ниже изображении раздел **Основное** свернут. 

    :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="Домашняя страница лаборатории":::

    > [!IMPORTANT] 
    > Не закрывайте эту страницу и выполните инструкции, приведенные в следующем разделе, чтобы удалить ненужные ресурсы и избежать затрат на работу лаборатории, а также виртуальной машины в Azure. Если вы хотите пройти следующее руководство, в рамках которого вы проверите доступ к виртуальной машине в лаборатории, очистку ресурсов лучше выполнить после этого. 

## <a name="cleanup-resources"></a>Очистка ресурсов

1. Прежде всего удалите лабораторию, чтобы вы могли удалить группу ресурсов. Вы не сможете удалить группу ресурсов, если в ней содержится лаборатория. Чтобы удалить лабораторию, нажмите кнопку **Удалить** на панели инструментов. 

    :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="Кнопка удаления лаборатории":::
 2. На странице с запросом подтверждения введите **имя лаборатории** и нажмите кнопку **Удалить**. 
 3. Дождитесь удаления лаборатории. Выберите значок **звонка**, чтобы просмотреть уведомления по операции удаления. Этот процесс занимает некоторое время. Подтвердите удаление лаборатории, а затем выберите **группу ресурсов** в меню навигации. 
 
    :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="Подтверждение удаления виртуальной машины в уведомлениях":::
 1. На странице **Группа ресурсов** выберите элемент **Удалить группу ресурсов** на панели инструментов. На странице с запросом подтверждения введите **имя группы ресурсов** и нажмите кнопку **Удалить**. Проверьте уведомления и убедитесь, что группа ресурсов успешно удалена.
 
    :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="Кнопка удаления группы ресурсов":::

## <a name="next-steps"></a>Дальнейшие действия
В рамках этого руководства вы создали лабораторию на виртуальной машине. Сведения о доступе к лаборатории см. в следующем руководстве:

> [!div class="nextstepaction"]
> [Руководство. Доступ к лаборатории](tutorial-use-custom-lab.md)

