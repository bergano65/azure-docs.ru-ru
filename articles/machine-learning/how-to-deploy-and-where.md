---
title: Как и где развертываются модели
titleSuffix: Azure Machine Learning
description: Узнайте, как и где развертываются модели Машинное обучение Azure, включая экземпляры контейнеров Azure, службу Azure Kubernetes, Azure IoT Edge и программируемые для полей массивы шлюзов.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
zone_pivot_groups: aml-control-methods
ms.openlocfilehash: 5e73744f3d467d08944d0e8800dd6d8824857abd
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87846995"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Развертывание моделей с помощью Машинного обучения Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Узнайте, как развернуть модель машинного обучения в качестве веб-службы в облаке Azure или на Azure IoT Edge устройствах.

Последовательность действий всегда одна и та же, независимо от того, где развертывается модель:

1. регистрация модели;
1. Подготовка конфигурации вывода
1. Подготовка скрипта записи (если [не используется развертывание без кода](how-to-deploy-no-code-deployment.md))
1. развертывание модели в целевом объекте вычислений;
1. Протестируйте развернутую модель, также называемую веб-службой.

Дополнительные сведения об основных понятиях, связанных с рабочим процессом развертывания, см. в разделе [Управление моделями, их развертывание и мониторинг с помощью машинное обучение Azure](concept-model-management-and-deployment.md).


 
::: zone pivot="cli"
[!INCLUDE [CLI quickstart](../../includes/machine-learning-how-to-deploy-and-where-cli.md)]
::: zone-end

::: zone pivot="py-sdk"
[!INCLUDE [SDK quickstart](../../includes/machine-learning-how-to-deploy-and-where-sdk.md)]
::: zone-end


### <a name="understanding-service-state"></a>Основные сведения о состоянии службы

Во время развертывания модели изменение состояния службы может отображаться, пока оно полностью развернуто.

В следующей таблице описаны различные состояния службы.

| Состояние WebService | Описание: | Конечное состояние?
| ----- | ----- | ----- |
| Переход | Служба находится в процессе развертывания. | Нет |
| Unhealthy | Служба была развернута, но сейчас недоступна.  | Нет |
| Непланируемый | В настоящее время служба не может быть развернута из-за нехватки ресурсов. | Нет |
| Сбой | Не удалось выполнить развертывание службы из-за ошибки или сбоя. | Да |
| Работоспособно | Служба работоспособна, и доступна конечная точка. | Да |


### <a name="batch-inference"></a><a id="azuremlcompute"></a>Вывод пакета
Машинное обучение Azure целевые объекты вычислений создаются и управляются с помощью Машинное обучение Azure. Их можно использовать для прогнозирования пакетной службы из Машинное обучение Azure конвейеров.

Пошаговое руководство по выводу пакетов с помощью Машинное обучение Azure COMPUTE см. [в разделе Выполнение пакетных прогнозов](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a>Вывод IoT Edge
Поддержка развертывания на границе доступна в предварительной версии. Дополнительные сведения см. в разделе [развертывание машинное обучение Azure как модуля IOT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a name="next-steps"></a>Дальнейшие шаги

* [Устранение неполадок при развертывании](how-to-troubleshoot-deployment.md)
* [развертывание в Службе Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).
* [Создание клиентских приложений для использования веб-служб](how-to-consume-web-service.md)
* [Обновление веб-службы](how-to-deploy-update-web-service.md)
* [Развертывание модели с помощью пользовательского образа DOCKER](how-to-deploy-custom-docker-image.md)
* [Использование TLS для защиты веб-службы с помощью Машинного обучения Azure](how-to-secure-web-service.md).
* [Мониторинг моделей Машинное обучение Azure с помощью Application Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)
* [Создание предупреждений и триггеров событий для развертываний моделей](how-to-use-event-grid.md)

