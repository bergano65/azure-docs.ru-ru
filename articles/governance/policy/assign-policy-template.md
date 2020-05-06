---
title: Краткое руководство. Создание назначения политики с помощью шаблонов
description: В этом кратком руководстве с помощью шаблона диспетчера ресурсов создается назначение политики для обнаружения ресурсов, не соответствующих требованиям.
ms.date: 03/16/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 646e2e064618e70fd9e9e747926f0886d85fde26
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82187789"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Краткое руководство. Создание назначения политики для идентификации несоответствующих требованиям ресурсов с помощью шаблона Resource Manager

Чтобы понять, соответствуют ли ресурсы требованиям в Azure, прежде всего нужно определить их состояние.
В этом кратком руководстве описано, как создать назначение политики для определения виртуальных машин, которые не используют управляемые диски. Завершив работу, вы узнаете, какие виртуальные машины не используют управляемые диски, так как _не соответствуют_ назначению политики.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="create-a-policy-assignment"></a>Создание назначения политики

С помощью этого краткого руководства вы создадите назначение политики и назначите встроенное определение политики _Аудит виртуальных машин, которые не используют управляемые диски_. См. [полный список всех доступных встроенных политик Azure](./samples/index.md).

### <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json" range="1-36" highlight="26-34":::

В шаблоне определен следующий ресурс:

- [Microsoft.Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

### <a name="deploy-the-template"></a>Развертывание шаблона

> [!NOTE]
> Служба "Политика Azure" предоставляется бесплатно. Дополнительные сведения см. в статье [Что такое служба "Политика Azure"?](./overview.md).

1. Выберите следующее изображение, чтобы войти на портал Azure и открыть шаблон:

   [![Развертывание шаблона политики в Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Введите или выберите следующие значения.

   | Имя | Значение |
   |------|-------|
   | Подписка | Выберите подписку Azure. |
   | Группа ресурсов | Выберите **Создать**, укажите имя, а затем выберите **OK**. На снимке экрана ниже группа ресурсов называется _mypolicyquickstart\<дата в формате ММДД\>rg_. |
   | Расположение | Выберите регион. Например, **центральная часть США**. |
   | Имя назначения политики | Укажите имя назначения политики. При необходимости можно использовать отображаемое имя определения политики. Например, **Аудит виртуальных машин, которые не используют управляемые диски**. |
   | Имя группы ресурсов | Укажите имя группы ресурсов, для которой вы хотите назначить политику. В этом кратком руководстве используйте значение по умолчанию **[resourceGroup().name]** . **[resourceGroup()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)**  — это функция шаблона, которая извлекает группу ресурсов. |
   | Идентификатор определения политики | Укажите **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**. |
   | Я принимаю указанные выше условия | Установите этот флажок. |

1. Щелкните **Приобрести**.

Ниже приведены некоторые дополнительные ресурсы.

- Дополнительные примеры шаблонов см. в статье о [шаблонах быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Справочник по шаблонам Azure можно просмотреть [здесь](/azure/templates/microsoft.authorization/allversions).
- Чтобы узнать, как создавать шаблоны Resource Manager, ознакомьтесь с [документацией по Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Чтобы узнать о развертывании на уровне подписки, ознакомьтесь со статьей [Создание групп ресурсов и ресурсов на уровне подписки](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Проверка развертывания

Выберите **Соответствие** в левой части страницы и найдите ранее созданное назначение политики **Аудит виртуальных машин, которые не используют управляемые диски**.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="Страница сведений о соответствии политике" border="false":::

Существующие ресурсы, которые не соответствуют новому назначению, отображаются в разделе **Несоответствующие ресурсы**.

Дополнительные сведения см. в разделе [Как работает соответствие](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить созданное назначение, выполните следующие действия:

1. Выберите элемент **Соответствие** (или **Назначения**) в левой части страницы службы Политика Azure и найдите ранее созданное назначение политики **Аудит виртуальных машин, которые не используют управляемые диски**.

1. Щелкните правой кнопкой мыши назначение политики **Аудит виртуальных машин, которые не используют управляемые диски** и выберите **Удалить назначение**.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Удаление назначения на странице сведений о соответствии" border="false":::

## <a name="next-steps"></a>Дальнейшие действия

С помощью этого краткого руководства вы назначили встроенное определение политики для области и изучили отчет о соответствии. Определение политики проверяет, все ли ресурсы в области соответствуют требованиям, и определяет, какие из них не соответствуют.

Следующее руководство серии содержит сведения о назначении политик для проверки новых ресурсов на соответствие требованиям:

> [!div class="nextstepaction"]
> [Создание политик и управление ими](./tutorials/create-and-manage.md)