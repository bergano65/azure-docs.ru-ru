---
title: Руководство по созданию рабочей области машинного обучения Azure с помощью шаблона Resource Manager
description: В этом учебнике вы с помощью шаблона Azure Resource Manager быстро развернете рабочую область Azure для машинного обучения.
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.openlocfilehash: 76f37beb22e28c0232efd0d62e82c8d3b60c78dc
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84345093"
---
# <a name="tutorial-deploy-an-azure-machine-learning-workspace-using-a-resource-manager-template"></a>Руководство по развертыванию рабочей области машинного обучения Azure с помощью шаблона Resource Manager
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этом руководстве показано, как создать рабочую область машинного обучения Azure с помощью шаблона Azure Resource Manager. Рабочие области машинного обучения Azure упорядочивают все ресурсы машинного обучения — от контрольных наборов данных до развернутых моделей. Рабочие области — это централизованное место для совместной работы с коллегами по созданию, запуску и просмотру экспериментов, управлению вычислительными ресурсами для обучения и получения выводов, а также по мониторингу и управлению версиями развернутых моделей.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://aka.ms/AMLFree), прежде чем начинать работу.

* Чтобы выполнять приведенные в этом документе команды CLI в **локальной среде**, вам потребуется [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Создание рабочей области

### <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-machine-learning-create/).

:::code language="json" source="~/quickstart-templates/101-machine-learning-create/azuredeploy.json" range="1-258" highlight="224-254":::

В шаблоне определены следующие ресурсы:

* [Microsoft.MachineLearningServices/workspaces](/azure/templates/microsoft.machinelearningservices/workspaces). Создайте рабочую область машинного обучения Azure. В этом шаблоне расположение и имя — это параметры, которые пользователь может передать или интерактивно ввести.

### <a name="deploy-the-template"></a>Развертывание шаблона 

Чтобы воспользоваться шаблоном в Azure CLI, войдите в систему и выберите свою подписку (см. статью [Вход с помощью Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)). Далее выполните:

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-machine-learning-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
workspaceName="${projectName}ws" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters workspaceName=$workspaceName location=$location && 
echo "Press [ENTER] to continue ..." &&
read
```

Перед выполнением приведенной выше команды укажите следующее:

1. Имя проекта, которое станет основой для имен создаваемой группы ресурсов и рабочей области машинного обучения Azure.
1. Расположение Azure, в котором вы хотите выполнить развертывание.

## <a name="review-deployed-resources"></a>Просмотр развернутых ресурсов

Чтобы просмотреть рабочую область машинного обучения Azure, сделайте следующее:

1. Перейдите на сайт https://portal.azure.com. 
1. Вход 
1. Выберите только что созданную рабочую область.

Вы увидите домашнюю страницу службы "Машинное обучение Azure". 

:::image type="content" source="media/tutorial-resource-manager-workspace/workspace-home.png" alt-text="Снимок экрана рабочей области машинного обучения Azure":::

Чтобы просмотреть все ресурсы, связанные с развертыванием, щелкните имя рабочей области в виде ссылки в левом верхнем углу экрана (`my_templated_ws` на снимке экрана выше). Эта ссылка ведет к группе ресурсов на портале Azure. Группа ресурсов называется `{projectName}rg`, а рабочая область — `{projectName}ws`.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если эта рабочая область вам не нужна, удалите ее. Так как она связана с другими ресурсами, например с учетной записью хранения, вы, вероятно, захотите удалить всю созданную группу ресурсов. Группу ресурсов можно удалить с помощью портала. Для этого нажмите кнопку "Удалить" и подтвердите удаление. Группу ресурсов можно удалить также с помощью CLI. 

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Дальнейшие действия

В ходе работы с этим руководством вы создали рабочую область машинного обучения Azure с помощью шаблона Azure Resource Manager. Если вы хотите продолжить изучение Машинного обучения Azure, перейдите к следующему руководству. 

> [!div class="nextstepaction"]
> [Руководство. Начало работы по созданию эксперимента машинного обучения с помощью пакета SDK для Python](tutorial-1st-experiment-sdk-setup.md)
