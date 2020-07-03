---
title: Краткое руководство. Создание общего запроса с помощью шаблонов
description: В этом кратком руководстве вы будете использовать шаблон Resource Manager, чтобы создать общий запрос Resource Graph, который подсчитывает количество виртуальных машины по типу ОС.
ms.date: 04/28/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 050cf26da2054883fceaa08b11f94c6af4c85a16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254245"
---
# <a name="quickstart-create-a-shared-query-by-using-a-resource-manager-template"></a>Краткое руководство. Создание общего запроса с помощью шаблона Resource Manager

Запрос Resource Graph можно сохранить в виде _закрытого_ или _общего запроса_. Закрытый запрос сохраняется в профиле портала пользователей и не виден другим пользователям. Общий запрос — это объект Resource Manager, который можно совместно использовать с другими пользователями посредством разрешений, или доступ на основе ролей. Общий запрос обеспечивает согласованность выполнения обнаружения ресурсов. В этом кратком руководстве для создания общего запроса используется шаблон Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="create-a-shared-query"></a>Создание общего запроса

В этом кратком руководстве вы создадите общий запрос _Число виртуальных машин по ОС_. Чтобы выполнить этот запрос в пакете SDK или на портале с помощью обозревателя Resource Graph, ознакомьтесь с разделом [Подсчет виртуальных машин по типу ОС](./samples/starter.md#count-os).

### <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/).

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json" highlight="28-37":::

В шаблоне определен следующий ресурс:

- [Microsoft.ResourceGraph/queries](/azure/templates/microsoft.resourcegraph/queries)

### <a name="deploy-the-template"></a>Развертывание шаблона

> [!NOTE]
> Azure Resource Graph — это бесплатная служба. Дополнительные сведения см. в статье [Что такое Azure Resource Graph?](./overview.md)

1. Выберите следующее изображение, чтобы войти на портал Azure и открыть шаблон:

   [![Развертывание шаблона политики в Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json)

1. Введите или выберите следующие значения.

   | Имя | Значение |
   |------|-------|
   | Подписка | Выберите подписку Azure. |
   | Группа ресурсов | Выберите **Создать**, укажите имя, а затем выберите **OK**. |
   | Расположение | Выберите регион. Например, **центральная часть США**. |
   | Имя запроса | Оставьте значение по умолчанию **Число виртуальных машин по ОС**. |
   | Код запроса | Оставьте значение по умолчанию `Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)`. |
   | Описание запроса | Оставьте значение по умолчанию **Этот общий запрос подсчитывает количество всех виртуальных машин и суммирует их по типу ОС**. |
   | Я принимаю указанные выше условия | Установите этот флажок. |

1. Щелкните **Приобрести**.

Ниже приведены некоторые дополнительные ресурсы.

- Дополнительные примеры шаблонов см. в статье о [шаблонах быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Справочник по шаблонам Azure можно просмотреть [здесь](/azure/templates/microsoft.resourcegraph/allversions).
- Чтобы узнать, как создавать шаблоны Resource Manager, ознакомьтесь с [документацией по Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Чтобы узнать о развертывании на уровне подписки, ознакомьтесь со статьей [Создание групп ресурсов и ресурсов на уровне подписки](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Проверка развертывания

Чтобы запустить общий запрос, выполните следующие действия.

1. На панели поиска портала найдите и выберите **Запросы Resource Graph**.

1. Выберите общий запрос с именем **Число виртуальных машин по ОС**, а затем перейдите на вкладку **Результаты** на странице **Обзор**.

Кроме того, общий запрос можно открыть из обозревателя Resource Graph.

1. На панели поиска портала найдите и выберите **Обозреватель Resource Graph**.

1. Нажмите кнопку **Открыть запрос**.

1. Измените **Тип** на _Общие запросы_. Если вы не видите **Число виртуальных машин по ОС** в списке, используйте поле фильтра для ограничения результатов. После отображения общего запроса **Число виртуальных машин по ОС** выберите его.

1. После загрузки запроса нажмите кнопку **Выполнить запрос**. Результаты отображаются на вкладке **Результаты** ниже.

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить созданный общий запрос, выполните следующие действия.

1. На панели поиска портала найдите и выберите **Запросы Resource Graph**.

1. Установите флажок для общего запроса с именем **Число виртуальных машин по ОС**.

1. Нажмите кнопку **Удалить** в верхней части страницы.

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы создали общий запрос Resource Graph.

Дополнительные сведения об общих запросах см. в следующей статье:

> [!div class="nextstepaction"]
> [Учебник. Создание запроса Azure Resource Graph и предоставление общего доступа к нему на портале Azure](./tutorials/create-share-query.md)