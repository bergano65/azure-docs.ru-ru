---
title: Включение Application Insights для службы машинного обучения Azure в рабочей среде
description: Узнайте, как настроить Application Insights для службы машинного обучения Azure, которая была развернута в службе Azure Kubernetes
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 10/01/2018
ms.openlocfilehash: 45871ab515c7ffd9520b1d77d3fd1e77abcc29ef
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114572"
---
# <a name="monitor-your-azure-machine-learning-models-in-production-with-application-insights"></a>Выполняйте мониторинг моделей службы машинного обучение Azure в рабочей среде с помощью Application Insights

В этой статье приведены сведения о том, как настроить **Application Insights** для использования в службе **Машинного обучения Azure**. После включения Application Insights можно использовать для отслеживания:
* Частоты запросов, времени отклика и частоты сбоев.
* Частоты зависимостей, времени отклика и частоты сбоев.
* Исключения

Дополнительные сведения см. в статье [Что такое Azure Application Insights?](../../application-insights/app-insights-overview.md) 

## <a name="prerequisites"></a>Предварительные требования
* Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начать работу.
* Должны быть установлены рабочая область машинного обучения Azure, локальный каталог со сценариями и пакет SDK машинного обучения Azure для Python. Дополнительные сведения о получении этих необходимых компонентов см. в документе [Настройка среды разработки](how-to-configure-environment.md).
* Обученная модель машинного обучения для развертывания в службе Azure Kubernetes (AKS). Если у вас ее нет, обратитесь к [руководству по обучению модели классификации изображений](tutorial-train-models-with-aml.md).
* [Кластер AKS](how-to-deploy-to-aks.md).

## <a name="enable--disable-in-the-portal"></a>Выполнение включения и отключения на портале

На портале Azure Application Insights можно включить или отключить.

### <a name="enable"></a>Включение

1. Откройте рабочую область на [портале Azure](https://portal.azure.com).

1. Перейдите к развертываниям и выберите службу, для которой требуется включить Application Insights.

   [![Развертывания](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Щелкните **Изменить** и перейдите к **Дополнительным параметрам**.

   [![Изменить](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. В **Дополнительных параметрах** выберите **Enable Application Insights diagnostics** (Включение диагностики Application Insights).

   [![Изменить](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Чтобы применить изменение, в верхней части экрана выберите **Изменить**. 

### <a name="disable"></a>Disable
Чтобы отключить Application Insights на портале Azure, сделайте следующее.

1. Войдите на портал Azure https://portal.azure.com.
1. Перейдите в рабочую область.
1. Выберите **Развертывания**, а затем **Выбрать службу** и **Изменить**.

   [![Изменить](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. В **Дополнительных параметрах** отмените выбранный параметр **Включить диагностику AppInsights**. 

   [![Снять](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Чтобы применить изменение, в верхней части экрана выберите **Изменить**. 

## <a name="enable--disable-from-the-sdk"></a>Выполнение включения и отключения из пакета SDK

### <a name="update-a-deployed-service"></a>Обновление развернутой службы
1. В рабочей среде следует найти требуемую службу (ws — имя рабочей среды)

    ```python
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Обновите службу и включите Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Трассировка пользовательских журналов в службе
Если требуется выполнять трассировку пользовательских журналов, см. статью [Развертывание моделей из службы машинного обучения Azure в службе Azure Kubernetes](how-to-deploy-to-aks.md). Результатом станет:

1. Обновление файла оценки путем добавления выражения print.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Обновление конфигурации AKS.
    
    ```python
    aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)
    ```

3. [Создание и развертывание образа.](how-to-deploy-to-aks.md)  

### <a name="disable-tracking-in-python"></a>Отключение наблюдения в Python

Чтобы отключить Application Insights, используйте следующий код:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    

## <a name="evaluate-data"></a>Данные оценки
Данные, полученные от службы, будут храниться в учетной записи Application Insights в той же группе ресурсов, в которой находится рабочая среда службы машинное обучение Azure.
Чтобы их просмотреть:
1. Перейдите к группе ресурсов на [портале Azure](https://portal.azure.com) и щелкните ресурс Application Insights. 
2. На вкладке **Общие сведения** отображается базовый набор метрик службы.

   [![Обзор](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Чтобы выполнить поиск в пользовательских трассировках, щелкните **Аналитика**.
4. В разделе "схема" щелкните **Трассировки** и затем **выполните** запрос. Данные должны появится ниже в формате таблицы. Также оны должны быть сопоставлены с настраиваемыми вызовами в файле оценки. 

   [![Пользовательские трассировки](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Чтобы получить дополнительные сведения об использовании Application Insights, см. статью [Что такое Azure Application Insights?](../../application-insights/app-insights-overview.md)
    

## <a name="example-notebook"></a>Пример записной книжки

В записной книжке [00.Getting Started/13.enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/13.enable-app-insights) показаны основные понятия из этой статьи.  Получите эту записную книжку:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дополнительная информация
Также данные можно собирать с моделей в рабочей среде. Ознакомьтесь со статьей [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md) 
