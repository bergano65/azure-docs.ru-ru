---
title: Настройка частной конечной точки
titleSuffix: Azure Machine Learning
description: Используйте частную ссылку Azure для безопасного доступа к рабочей области Машинное обучение Azure из виртуальной сети.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/30/2020
ms.openlocfilehash: 4ba7ec73ac70723e21b6acad571d62d14edd250a
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828129"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Настройка частной ссылки Azure для Машинное обучение Azure рабочей области

В этом документе вы узнаете, как использовать частную связь Azure с рабочей областью Машинное обучение Azure. Сведения о создании виртуальной сети для Машинное обучение Azure см. в статье [Общие сведения о изоляции и конфиденциальности виртуальной сети](how-to-network-security-overview.md) .

Частная ссылка Azure позволяет подключаться к рабочей области с помощью частной конечной точки. Частная конечная точка — это набор частных IP-адресов в виртуальной сети. Затем можно ограничить доступ к рабочей области, чтобы она выполнялась только по частным IP-адресам. Частная ссылка помогает снизить риск утечка данных. Дополнительные сведения о частных конечных точках см. в статье [Приватный канал Azure](/azure/private-link/private-link-overview).

> [!IMPORTANT]
> Частная ссылка Azure не влияет на плоскость управления Azure (операции управления), такие как удаление рабочей области или управление ресурсами вычислений. Например, создание, обновление или удаление целевого объекта вычислений. Эти операции выполняются через общедоступный Интернет в нормальном режиме. Операции с плоскостью данных, такие как использование Машинное обучение Azure Studio, API (включая опубликованные конвейеры) или пакет SDK, используют закрытую конечную точку.
>
> При использовании Mozilla Firefox могут возникнуть проблемы при попытке доступа к закрытой конечной точке рабочей области. Эта проблема может быть связана с DNS по протоколу HTTPS в Mozilla. В качестве обходного пути рекомендуется использовать Microsoft ребро Google Chrome.

## <a name="prerequisites"></a>Предварительные требования

Если вы планируете использовать рабочую область с включенной частной связью с ключом, управляемым клиентом, необходимо запросить эту функцию с помощью запроса в службу поддержки. Дополнительные сведения см. в статье [Управление квотами и их увеличение](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

## <a name="limitations"></a>Ограничения

Использование рабочей области Машинное обучение Azure с частной ссылкой недоступна в регионах Azure для государственных организаций или в регионах Azure для Китая.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Создание рабочей области, использующей закрытую конечную точку

Используйте один из следующих методов, чтобы создать рабочую область с частной конечной точкой:

> [!TIP]
> При необходимости шаблон Azure Resource Manager может создать новую виртуальную сеть. Для всех остальных методов требуется существующая виртуальная сеть.

# <a name="resource-manager-template"></a>[Шаблон Resource Manager](#tab/azure-resource-manager)

Шаблон Azure Resource Manager в [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) предоставляет простой способ создания рабочей области с частной конечной точкой и виртуальной сетью.

Сведения об использовании этого шаблона, включая частные конечные точки, см. в разделе [Использование шаблона Azure Resource Manager для создания рабочей области для машинное обучение Azure](how-to-create-workspace-template.md).

# <a name="python"></a>[Python](#tab/python)

Пакет SDK для Машинное обучение Azure Python предоставляет класс [приватиндпоинтконфиг](https://docs.microsoft.com/python/api/azureml-core/azureml.core.privateendpointconfig?view=azure-ml-py) , который можно использовать с [рабочей областью. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) для создания рабочей области с закрытой конечной точкой. Для этого класса требуется существующая виртуальная сеть.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Расширение Azure CLI для машинного обучения](reference-azure-machine-learning-cli.md) предоставляет команду [AZ ML Workspace Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext_azure_cli_ml_az_ml_workspace_create) . Следующие параметры этой команды можно использовать для создания рабочей области с частной сетью, но для нее требуется существующая виртуальная сеть.

* `--pe-name`— Имя создаваемой частной конечной точки.
* `--pe-auto-approval`— Следует ли автоматически утверждать подключения частной конечной точки к рабочей области.
* `--pe-resource-group`: Группа ресурсов для создания частной конечной точки в. Должна быть той же группой, которая содержит виртуальную сеть.
* `--pe-vnet-name`— Существующая виртуальная сеть для создания частной конечной точки в.
* `--pe-subnet-name`— Имя подсети, в которой создается частная конечная точка. Значение по умолчанию — `default`.

# <a name="portal"></a>[Портал](#tab/azure-portal)

Вкладка " __сеть__ " в машинное обучение Azure Studio позволяет настроить частную конечную точку. Однако для этого требуется существующая виртуальная сеть. Дополнительные сведения см. [в статье Создание рабочих областей на портале](how-to-manage-workspace.md).

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Использование рабочей области в частной конечной точке

Так как взаимодействие с рабочей областью разрешено только из виртуальной сети, все среды разработки, использующие эту рабочую область, должны быть членами виртуальной сети. Например, виртуальная машина в виртуальной сети.

> [!IMPORTANT]
> Чтобы избежать временного сбоя подключения, корпорация Майкрософт рекомендует сбрасывать кэш DNS на компьютерах, подключающихся к рабочей области, после включения частной связи. 

Сведения о виртуальных машинах Azure см. в [документации по виртуальным машинам](/azure/virtual-machines/).


## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о защите рабочей области Машинное обучение Azure см. в статье [Общие сведения о изоляции и конфиденциальности виртуальной сети](how-to-network-security-overview.md) .

* Если вы планируете использовать настраиваемое решение DNS в виртуальной сети, см. статью [Использование рабочей области с пользовательским DNS-сервером](how-to-custom-dns.md).
