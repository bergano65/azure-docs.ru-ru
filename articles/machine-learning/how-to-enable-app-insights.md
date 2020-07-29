---
title: Мониторинг и получение данных из конечных точек веб-службы Машинное обучение
titleSuffix: Azure Machine Learning
description: Мониторинг веб-служб, развернутых с помощью Машинное обучение Azure Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: 2bc3eb72ff0c5d29fd72de848abf87dfe84e2a01
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320227"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Мониторинг и сбор данных из конечных точек веб-службы Машинного обучения
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете, как получать данные из и отслеживать модели, развернутые в конечных точках веб-службы в службе Azure Kubernetes Service (AKS), или службу "экземпляры контейнеров Azure" (ACI), включив Application Insights Azure через 
* [Пакет SDK Python для Машинного обучения Azure](#python)
* [Машинное обучение Azure Studio](#studio) вhttps://ml.azure.com

Помимо сбора выходных данных и ответов конечной точки, можно отслеживать:

* Частоты запросов, времени отклика и частоты сбоев.
* Частоты зависимостей, времени отклика и частоты сбоев.
* Исключения

Дополнительные [сведения об Azure Application Insights](../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Опробуйте [бесплатную или платную версию машинное обучение Azure](https://aka.ms/AMLFree) уже сегодня

* Должны быть установлены рабочая область машинного обучения Azure, локальный каталог со скриптами и пакет SDK машинного обучения Azure для Python. Чтобы узнать, как выполнить эти предварительные требования, см. статью [Настройка среды разработки](how-to-configure-environment.md) .

* Обученная модель машинного обучения для развертывания в службе Azure Kubernetes (AKS) или в экземпляре контейнера Azure (ACI). Если у вас ее нет, см. Руководство [обучение модели классификации изображений](tutorial-train-models-with-aml.md)

## <a name="web-service-metadata-and-response-data"></a>Метаданные веб-службы и данные ответа

> [!IMPORTANT]
> Azure Application Insights записывает в журнал только полезные данные размером до 64 КБ. При достижении этого предела могут возникнуть ошибки, например нехваткой памяти, или нет сведений, которые могут быть занесены в журнал.

Чтобы заносить в журнал сведения о запросе к веб-службе, добавьте `print` инструкции в файл Score.py. Каждая `print` инструкция приводит к одной записи в таблице трассировки в Application Insights, под сообщением `STDOUT` . Содержимое `print` инструкции будет находиться в разделе `customDimensions` , а затем `Contents` в таблице трассировки. При печати строки JSON она создает иерархическую структуру данных в выходных данных трассировки в разделе `Contents` .

Вы можете запросить Azure Application Insights напрямую для доступа к этим данным или настроить [непрерывный экспорт](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) в учетную запись хранения для более длительного хранения или дальнейшей обработки. Данные модели можно использовать в Машинное обучение Azure для настройки меток, переобучения, пояснения, анализа данных или других способов использования. 

<a name="python"></a>

## <a name="use-python-sdk-to-configure"></a>Использование пакета SDK для Python для настройки 

### <a name="update-a-deployed-service"></a>Обновление развернутой службы

1. Найдите службу в рабочей области. Значение для `ws` — это имя рабочей области.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Обновление службы и включение Azure Application Insights

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Трассировка пользовательских журналов в службе

Если требуется вести журнал пользовательских трассировок, выполните инструкции из руководства по стандартному процессу развертывания для AKS или ACI, представленные в документе [Deploy models with the Azure Machine Learning service](how-to-deploy-and-where.md) (Развертывание моделей с помощью Службы машинного обучения Azure). Затем выполните следующие действия:

1. Чтобы отправить данные в Application Insights во время вывода, обновите файл оценки, добавив инструкции Print. Для записи более сложных сведений, например данных запроса и ответа, мы будем использовать структуру JSON. В следующем примере score.py файл регистрирует время инициализации модели, входные и выходные данные во время вывода, а также время возникновения ошибок.

    > [!IMPORTANT]
    > Azure Application Insights записывает в журнал только полезные данные размером до 64 КБ. При достижении этого предела могут возникнуть ошибки, например нехваткой памяти, или нет сведений, которые могут быть занесены в журнал. Если данные, которые нужно заносить в журнал, имеют больший размер в 64 КБ, следует сохранить их в хранилище BLOB-объектов, используя сведения из раздела " [Получение данных для моделей в рабочей среде](how-to-enable-data-collection.md)".
    
    ```python
    import pickle
    import json
    import numpy 
    from sklearn.externals import joblib
    from sklearn.linear_model import Ridge
    from azureml.core.model import Model
    import time

    def init():
        global model
        #Print statement for appinsights custom traces:
        print ("model initialized" + time.strftime("%H:%M:%S"))
        
        # note here "sklearn_regression_model.pkl" is the name of the model registered under the workspace
        # this call should return the path to the model.pkl file on the local disk.
        model_path = Model.get_model_path(model_name = 'sklearn_regression_model.pkl')
        
        # deserialize the model file back into a sklearn model
        model = joblib.load(model_path)
    

    # note you can pass in multiple rows for scoring
    def run(raw_data):
        try:
            data = json.loads(raw_data)['data']
            data = numpy.array(data)
            result = model.predict(data)
            # Log the input and output data to appinsights:
            info = {
                "input": raw_data,
                "output": result.tolist()
                }
            print(json.dumps(info))
            # you can return any datatype as long as it is JSON-serializable
            return result.tolist()
        except Exception as e:
            error = str(e)
            print (error + time.strftime("%H:%M:%S"))
            return error
    ```

2. Обновление конфигурации службы
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Создайте образ и разверните его в [AKS или ACI](how-to-deploy-and-where.md).

### <a name="disable-tracking-in-python"></a>Отключение наблюдения в Python

Чтобы отключить Application Insights Azure, используйте следующий код:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="use-azure-machine-learning-studio-to-configure"></a>Использование Машинное обучение Azure Studio для настройки

Вы также можете включить Azure Application Insights из Машинное обучение Azure Studio, когда будете готовы к развертыванию модели с помощью этих шагов.

1. Войдите в рабочую область по адресуhttps://ml.azure.com/
1. Перейдите к разделу **модели** и выберите модель, которую требуется развернуть.
1. Выбрать **+ развернуть**
1. Заполнение формы « **развертывание модели** »
1. Развернуть меню " **Дополнительно** "

    ![Форма развертывания](./media/how-to-enable-app-insights/deploy-form.png)
1. Выберите **включить диагностику Application Insights и сбор данных**

    ![Включить App Insights](./media/how-to-enable-app-insights/enable-app-insights.png)

## <a name="view-metrics-and-logs"></a>Просмотр метрик и журналов

Данные службы хранятся в учетной записи Azure Application Insights в той же группе ресурсов, что и Машинное обучение Azure.
Чтобы их просмотреть:

1. Перейдите в рабочую область Машинное обучение Azure в [студии](https://ml.azure.com/).
1. Выберите **Конечные точки**.
1. Выберите развернутую службу.
1. Прокрутите вниз, чтобы найти **URL-адрес Application Insights** , и щелкните ссылку.

    [![Указать URL-адрес Application Insights](./media/how-to-enable-app-insights/appinsightsloc.png)](././media/how-to-enable-app-insights/appinsightsloc.png#lightbox)

1. В Исигхтс приложений на вкладке **Обзор** или в разделе __мониторинг__ в списке слева выберите __журналы__.

    [![Вкладка "Обзор" в разделе "Мониторинг"](./media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Чтобы просмотреть данные, записанные в журнал из файла score.py, просмотрите таблицу __traces__ . Следующий запрос выполняет поиск журналов, в которых было записано __входное__ значение:

    ```kusto
    traces
    | where customDimensions contains "input"
    | limit 10
    ```

   [![данные трассировки](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)

Дополнительные сведения об использовании Application Insights Azure см. в разделе [что такое Application Insights?](../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Экспорт данных для дальнейшей обработки и более длительного хранения

>[!Important]
> Application Insights Azure поддерживает только экспорты в хранилище BLOB-объектов. Дополнительные ограничения этой возможности экспорта перечислены в окне [Экспорт данных телеметрии из App Insights](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration).

Вы можете использовать [непрерывный экспорт](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) Azure Application Insights для отправки сообщений в поддерживаемую учетную запись хранения, где можно задать более длительное хранение. Данные хранятся в формате JSON и могут быть легко проанализированы для извлечения данных модели. 

Фабрика данных Azure, конвейеры машинного обучения Azure или другие средства обработки данных можно использовать для преобразования данных по мере необходимости. После преобразования данных их можно зарегистрировать в рабочей области Машинное обучение Azure в качестве набора данных. Дополнительные сведения см. в разделе [Создание и регистрация наборов данных](how-to-create-register-datasets.md).

:::image type="content" source="media/how-to-enable-app-insights/continuous-export-setup.png" alt-text="непрерывный экспорт.":::


## <a name="example-notebook"></a>Пример записной книжки

В записной книжке [Enable-App-Insights-in-Production-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) описываются концепции, описанные в этой статье. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, [как развернуть модель в кластере службы Azure Kubernetes](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) или [как развернуть модель в службе "экземпляры контейнеров Azure](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) ", чтобы развернуть модели в конечных точках веб-службы и разрешить Azure Application Insights использовать сбор данных и мониторинг конечных точек.
* Дополнительные сведения об использовании данных, собираемых из моделей в рабочей среде, см. в разделе [млопс. Управление, развертывание и мониторинг моделей с помощью машинное обучение Azure](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) . Такие данные позволяют постоянно улучшать процесс машинного обучения.
