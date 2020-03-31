---
title: развертывание и использование
titleSuffix: ML Studio (classic) - Azure
description: Для развертывания рабочих процессов и моделей машинного обучения в качестве веб-сервисов можно использовать программу Azure Machine Learning Studio (классическая). Затем эти веб-службы можно использовать для вызова моделей машинного обучения из приложений в Интернете, чтобы делать прогнозы в режиме реального времени или в пакетном режиме.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: ff6ae0de0bbd8c47b81fa5066a97eb0b3e0cf6bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204398"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Студия машинного обучения Azure (классические) веб-службы: развертывание и потребление

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Для развертывания рабочих процессов и моделей машинного обучения в качестве веб-сервисов можно использовать программу Azure Machine Learning Studio (классическая). Затем эти веб-службы можно использовать для вызова моделей машинного обучения из приложений в Интернете, чтобы делать прогнозы в режиме реального времени или в пакетном режиме. Так как это веб-службы RESTful, их можно вызывать, используя различные языки программирования и платформы, например .NET и Java, а также приложения, например Microsoft Excel.

В следующих разделах представлены ссылки на пошаговые инструкции, код и документацию, которые помогут вам приступить к работе.

## <a name="deploy-a-web-service"></a>Развертывание веб-службы

### <a name="with-azure-machine-learning-studio-classic"></a>С Azure машинного обучения Studio (классический)

Портал Studio (классический) и веб-сервис Microsoft Azure Machine Learning Web Services помогают развертывать веб-службу и управлять им без написания кода.

В статьях по следующим ссылкам содержатся общие сведения о процессе развертывания новой веб-службы:

* Общие сведения о развертывании новой веб-службы на основе Azure Resource Manager см. в статье [Развертывание новой веб-службы](deploy-a-machine-learning-web-service.md).
* Пошаговое руководство по развертыванию веб-службы см. в статье [Развертывание веб-службы машинного обучения Azure](deploy-a-machine-learning-web-service.md).
* Для полного пошагового руководства о том, как создать и развернуть веб-сервис, начните с [учебника 1: Предсказать кредитный риск.](tutorial-part1-credit-risk.md)
* Конкретные примеры развертывания веб-службы см. в следующих статьях:

  * [Учебник 3: Развертывание модели кредитного риска](tutorial-part3-credit-risk-deploy.md)
  * [Как развернуть веб-службу в нескольких регионах](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>С помощью интерфейсов API поставщика ресурсов веб-служб (интерфейсов API Azure Resource Manager)

Студия машинного обучения Azure (классический) поставщик ресурсов для веб-сервисов позволяет развертывать и управляться веб-сервисами с помощью вызовов REST API. Дополнительные сведения см. в статье [Azure Machine Learning Studio Management REST APIs](/rest/api/machinelearning/index) (REST API для управления Студией машинного обучения Azure).

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>С помощью командлетов PowerShell

Студия машинного обучения Azure (классический) поставщик ресурсов для веб-сервисов позволяет развертывать и управляться веб-сервисами с помощью cmdlets PowerShell.

Чтобы использовать cmdlets, необходимо сначала войти в свою учетную запись Azure из среды PowerShell с помощью cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Если вы не знакомы с вызовом команд PowerShell на основе Resource Manager, см. статью [Использование Azure PowerShell с Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md).

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

* [Студия машинного обучения Azure (классическая) ссылка Cmdlets](https://docs.microsoft.com/powershell/module/az.machinelearning) на MSDN
* Пример [пошагового руководства](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) на сайте GitHub.

## <a name="consume-the-web-services"></a>Использование веб-служб

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Через пользовательский интерфейс веб-служб Машинного обучения Azure (тестирование)

Веб-службу можно проверить на портале веб-служб Машинного обучения Azure. Эта проверка включает в себя тестирование интерфейсов службы запрос-ответ (RRS) и службы пакетного выполнения (BES).

* [Развертывание новой веб-службы](deploy-a-machine-learning-web-service.md)
* [Развертывание веб-службы машинного обучения Azure](deploy-a-machine-learning-web-service.md)
* [Учебник 3: Развертывание модели кредитного риска](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Из Excel

Вы можете скачать шаблон Excel, который использует веб-службу:

* [Использование веб-службы Машинного обучения Azure в Excel](consuming-from-excel.md)
* [Дополнение Excel для веб-служб машинного обучения Azure](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Из клиента на основе REST

Веб-службы Машинного обучения Azure представляют собой интерфейсы API RESTful. Вы можете использовать эти AAP с различных платформ, таких как .NET, Python, R, Java и т.д. Страница **Потребления** для веб-службы на [веб-портале Microsoft Azure Machine Learning Имеет](https://services.azureml.net) пример кода, который может помочь вам начать работу. Дополнительные сведения см. в статье [Как использовать веб-службу машинного обучения Azure, развернутую из эксперимента машинного обучения](consume-web-services.md).
