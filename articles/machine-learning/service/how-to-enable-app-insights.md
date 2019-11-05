---
title: Мониторинг и получение данных из конечных точек веб-службы Машинное обучение
titleSuffix: Azure Machine Learning
description: Мониторинг веб-служб, развернутых с помощью Машинное обучение Azure Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: lostmygithubaccount
ms.date: 10/11/2019
ms.custom: seoapril2019
ms.openlocfilehash: 9da057683f3da41f077b309db79271a10738b59d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490007"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Мониторинг и получение данных из конечных точек веб-службы ML
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете, как получать данные из и отслеживать модели, развернутые в конечных точках веб-службы в службе Azure Kubernetes Service (AKS) или экземплярах контейнеров Azure (ACI), включив Azure Application Insights. Помимо сбора входных данных и ответов конечной точки, вы можете отслеживать:
* частоты запросов, времени отклика и частоты сбоев;
* частоты зависимостей, времени отклика и частоты сбоев;
* Исключения.

Дополнительные [сведения об Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Технические условия

* Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию машинное обучение Azure](https://aka.ms/AMLFree) уже сегодня.

* Должны быть установлены рабочая область машинного обучения Azure, локальный каталог со скриптами и пакет SDK машинного обучения Azure для Python. В руководстве по [настройке среды разработки](how-to-configure-environment.md) описано, как получить эти обязательные компоненты.
* Обученная модель машинного обучения для развертывания в службе Azure Kubernetes (AKS) или в экземпляре контейнера Azure (ACI). Если у вас ее нет, см. руководство по [обучению модели классификации изображений](tutorial-train-models-with-aml.md).

## <a name="web-service-input-and-response-data"></a>Входные и ответные данные веб-службы

Ввод и ответ службы, соответствующие входным данным для модели ML и ее прогнозов, записываются в трассировку Application Insights Azure в `"model_data_collection"`. Вы можете запросить Azure Application Insights напрямую для доступа к этим данным или настроить [непрерывный экспорт](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) в учетную запись хранения для более длительного хранения или дальнейшей обработки. Затем данные модели можно использовать в службе машинного обучения Azure для настройки меток, переобучения, объяснения, анализа данных или других способов использования. 

## <a name="use-the-azure-portal-to-configure"></a>Использование портал Azure для настройки

Вы можете включить и отключить Application Insights Azure в портал Azure. 

1. Откройте рабочую область на [портале Azure](https://portal.azure.com).

1. На вкладке **развертывания** выберите службу, в которой вы хотите включить Application Insights Azure.

   [![Список служб на вкладке "Развертывания"](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Выберите **Изменить**

   [![Кнопка "Изменить"](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. В разделе **Дополнительные параметры** установите флажок **Включить диагностику AppInsights**.

   [![Установленный флажок для включения диагностики](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Чтобы применить изменение, в верхней части экрана выберите **Изменить**. 

### <a name="disable"></a>Disable
1. Откройте рабочую область на [портале Azure](https://portal.azure.com).
1. Выберите **развертывания**, выберите службу и нажмите кнопку **изменить**.

   [![Нажмите кнопку "Изменить"](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox).

1. В разделе **Дополнительные параметры** снимите флажок **Включить диагностику AppInsights**. 

   [![Снятый флажок включения диагностики](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Чтобы применить изменение, в верхней части экрана выберите **Изменить**. 
 
## <a name="use-python-sdk-to-configure"></a>Использование пакета SDK для Python для настройки 

### <a name="update-a-deployed-service"></a>Обновление развернутой службы
1. Найдите службу в рабочей области. Значение `ws` обозначает имя рабочей области.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Обновите службу и включите Azure Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Трассировка пользовательских журналов в службе
Если требуется вести журнал пользовательских трассировок, выполните инструкции из руководства по стандартному процессу развертывания для AKS или ACI, представленные в документе [Deploy models with the Azure Machine Learning service](how-to-deploy-and-where.md) (Развертывание моделей с помощью Службы машинного обучения Azure). Затем выполните следующие действия:

1. измените файл оценки, добавив выражения print;
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Обновите конфигурацию службы.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Создайте образ и разверните его в [AKS](how-to-deploy-to-aks.md) или [ACI](how-to-deploy-to-aci.md).  

### <a name="disable-tracking-in-python"></a>Отключение наблюдения в Python

Чтобы отключить Application Insights Azure, используйте следующий код:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    
## <a name="use-studio-to-configure"></a>Использование Studio для настройки

Вы можете включать и отключать Application Insights в Машинное обучение Azure Studio.

1. В [машинное обучение Azure Studio](https://ml.azure.com)откройте рабочую область.

1. На вкладке **Развертывания** выберите службу, для которой требуется включить Application Insights.

   [![Список служб на вкладке "Развертывания"](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Выберите **Изменить**

   [![Кнопка "Изменить"](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. В разделе **Дополнительные параметры** установите флажок **Включить диагностику AppInsights**.

   [![Установленный флажок для включения диагностики](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Чтобы применить изменение, в верхней части экрана выберите **Изменить**. 

### <a name="disable"></a>Disable
1. В [машинное обучение Azure Studio](https://ml.azure.com)откройте рабочую область.
1. Откройте **Развертывания**, затем выберите службу и щелкните **Изменить**.

   [![Нажмите кнопку "Изменить"](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox).

1. В разделе **Дополнительные параметры** снимите флажок **Включить диагностику AppInsights**. 

   [![Снятый флажок включения диагностики](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Чтобы применить изменение, в верхней части экрана выберите **Изменить**. 
 

## <a name="evaluate-data"></a>Данные оценки
Данные службы хранятся в учетной записи Azure Application Insights в той же группе ресурсов, что и Машинное обучение Azure.
Чтобы их просмотреть:
1. Перейдите в рабочую область службы Машинное обучение в [машинное обучение Azure Studio](https://ml.azure.com) и щелкните ссылку Application Insights.

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Выберите вкладку **Обзор**, чтобы увидеть базовый набор метрик для своей службы.

   [![Обзор](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Чтобы просмотреть полезные данные веб-службы и ответа, выберите **аналитика** .
1. В разделе Схема выберите **трассировки** и отфильтруйте трассировки с помощью сообщения `"model_data_collection"`. В пользовательских измерениях можно увидеть входные данные, прогнозы и другие релевантные сведения.

   [![данных модели](media/how-to-enable-app-insights/model-data-trace.png)](./media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Чтобы выполнить поиск в пользовательских трассировках, выберите **Аналитика**.
4. В разделе схемы выберите **Трассировки**. Затем выберите **Запуск**, чтобы выполнить запрос. Данные будут отображаться в формате таблицы и сопоставляться с пользовательскими вызовами в файле оценки. 

   [![Пользовательские трассировки](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Дополнительные сведения об использовании Application Insights Azure см. в разделе [что такое Application Insights?](../../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Экспорт данных для дальнейшей обработки и более длительного хранения

Вы можете использовать [непрерывный экспорт](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) Azure Application Insights для отправки сообщений в поддерживаемую учетную запись хранения, где можно задать более длительное хранение. Сообщения `"model_data_collection"` хранятся в формате JSON и могут быть легко проанализированы для извлечения данных модели. Фабрика данных Azure, конвейеры машинного обучения Azure или другие средства обработки данных можно использовать для преобразования данных по мере необходимости. После преобразования данных их можно зарегистрировать в рабочей области Машинное обучение Azure в качестве набора данных. Дополнительные сведения см. в разделе [Создание и регистрация наборов данных](how-to-create-register-datasets.md).

   [![непрерывного экспорта](media/how-to-enable-app-insights/continuous-export-setup.png)](./media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Пример записной книжки

В записной книжке [Enable-App-Insights-in-Production-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) описываются концепции, описанные в этой статье. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, [как развернуть модель в кластере службы Azure Kubernetes](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-kubernetes-service) или [как развернуть модель в службе "экземпляры контейнеров Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-container-instance) ", чтобы развернуть модели в конечных точках веб-службы и разрешить Azure Application Insights использовать сбор данных и конечную точку. производительность.
* Дополнительные сведения об использовании данных, собираемых из моделей в рабочей среде, см. в разделе [млопс. Управление, развертывание и мониторинг моделей с помощью машинное обучение Azure](https://docs.microsoft.com/azure/machine-learning/service/concept-model-management-and-deployment) . Такие данные позволяют постоянно улучшать процесс машинного обучения. 
