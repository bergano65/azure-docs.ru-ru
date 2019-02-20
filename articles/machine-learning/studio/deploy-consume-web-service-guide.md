---
title: развертывание и использование
titleSuffix: Azure Machine Learning Studio
description: Служба "Машинное обучение Azure" позволяет развертывать рабочие процессы и модели машинного обучения в качестве веб-служб. Затем эти веб-службы можно использовать для вызова моделей машинного обучения из приложений в Интернете, чтобы делать прогнозы в режиме реального времени или в пакетном режиме.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: e2add42e56f939dbfeb33ac2774d404411b1fefb
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997257"
---
# <a name="azure-machine-learning-studio-web-services-deployment-and-consumption"></a>Веб-службы Студии машинного обучения Azure: развертывание и использование
Служба "Машинное обучение Azure" позволяет развертывать рабочие процессы и модели машинного обучения в качестве веб-служб. Затем эти веб-службы можно использовать для вызова моделей машинного обучения из приложений в Интернете, чтобы делать прогнозы в режиме реального времени или в пакетном режиме. Так как это веб-службы RESTful, их можно вызывать, используя различные языки программирования и платформы, например .NET и Java, а также приложения, например Microsoft Excel.

В следующих разделах представлены ссылки на пошаговые инструкции, код и документацию, которые помогут вам приступить к работе.

## <a name="deploy-a-web-service"></a>Развертывание веб-службы

### <a name="with-azure-machine-learning-studio"></a>С помощью Студии машинного обучения Azure
На портале Студии машинного обучения и веб-служб Машинного обучения Microsoft Azure можно развернуть веб-службы, а также управлять ими без написания кода.

В статьях по следующим ссылкам содержатся общие сведения о процессе развертывания новой веб-службы:

* Общие сведения о развертывании новой веб-службы на основе Azure Resource Manager см. в статье [Развертывание новой веб-службы](publish-a-machine-learning-web-service.md).
* Пошаговое руководство по развертыванию веб-службы см. в статье [Развертывание веб-службы машинного обучения Azure](publish-a-machine-learning-web-service.md).
* Полное пошаговое руководство по созданию и развертыванию веб-службы см. в статье [Руководство 1. Прогнозирование кредитного риска](tutorial-part1-credit-risk.md).
* Конкретные примеры развертывания веб-службы см. в следующих статьях:

  * [Руководство 3. Развертывание модели кредитных рисков в Студии машинного обучения Azure](tutorial-part3-credit-risk-deploy.md).
  * [Развертывание веб-службы в нескольких регионах](how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>С помощью интерфейсов API поставщика ресурсов веб-служб (интерфейсов API Azure Resource Manager)
Поставщик ресурсов Машинного обучения Azure для веб-служб позволяет развертывать веб-службы и управлять ими с помощью вызовов REST API. Дополнительные сведения см. в статье [Machine Learning Web Service (REST)](/rest/api/machinelearning/index) (Веб-служба машинного обучения (REST)).

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->


### <a name="with-powershell-cmdlets"></a>С помощью командлетов PowerShell
Поставщик ресурсов Машинного обучения Azure для веб-служб позволяет развертывать веб-службы и управлять ими с помощью командлетов PowerShell.

Чтобы использовать командлеты, необходимо сначала войти в учетную запись Azure в среде PowerShell с помощью командлета [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount). Если вы не знакомы с вызовом команд PowerShell на основе Resource Manager, см. статью [Использование Azure PowerShell с Azure Resource Manager](../../azure-resource-manager/powershell-azure-resource-manager.md).

Чтобы экспортировать прогнозный эксперимент, используйте [этот пример кода](https://github.com/ritwik20/AzureML-WebServices). После создания EXE-файла из кода можно ввести следующую команду:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

При запуске приложения создается шаблон JSON веб-службы. Чтобы использовать шаблон для развертывания веб-службы, необходимо добавить следующие сведения:

* Имя и ключ учетной записи хранения.

    Их можно получить на [портале Azure](https://portal.azure.com/).
* Идентификатор плана предложения.

    Идентификатор плана можно получить на портале [веб-служб машинного обучения Azure](https://services.azureml.net). Для этого необходимо войти на портал и щелкнуть имя плана.

Добавьте их в шаблон JSON в качестве дочерних элементов узла *Properties* на том же уровне, где находится узел *MachineLearningWorkspace*.

Ниже приведен пример:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Дополнительные сведения см. в следующих статьях и примерах кода:

* [Azure Machine Learning Cmdlets](https://docs.microsoft.com/powershell/module/azurerm.machinelearning) (Командлеты Машинного обучения Azure) на сайте MSDN.
* Пример [пошагового руководства](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) на сайте GitHub.

## <a name="consume-the-web-services"></a>Использование веб-служб
### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Через пользовательский интерфейс веб-служб Машинного обучения Azure (тестирование)
Веб-службу можно проверить на портале веб-служб Машинного обучения Azure. Эта проверка включает в себя тестирование интерфейсов службы запрос-ответ (RRS) и службы пакетного выполнения (BES).

* [Развертывание новой веб-службы](publish-a-machine-learning-web-service.md)
* [Развертывание веб-службы машинного обучения Azure](publish-a-machine-learning-web-service.md)
* [Руководство 3. Развертывание модели кредитных рисков в Студии машинного обучения Azure](tutorial-part3-credit-risk-deploy.md).

### <a name="from-excel"></a>Из Excel
Вы можете скачать шаблон Excel, который использует веб-службу:

* [Использование веб-службы Машинного обучения Azure в Excel](consuming-from-excel.md)
* [Надстройка Excel для веб-служб машинного обучения Azure](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Из клиента на основе REST
Веб-службы Машинного обучения Azure представляют собой интерфейсы API RESTful. Их можно использовать на различных платформах, например .NET, Python, R, Java и т. д. На странице **Consume** (Использование) для веб-службы на [портале веб-служб машинного обучения Microsoft Azure](https://services.azureml.net) размещен пример кода, позволяющий приступить к работе. Дополнительные сведения см. в статье [Как использовать веб-службу машинного обучения Azure, развернутую из эксперимента машинного обучения](consume-web-services.md).
