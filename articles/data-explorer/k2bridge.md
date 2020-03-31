---
title: Визуализация данных от Azure Data Explorer с помощью Kibana
description: В этой статье вы узнаете, как настроить Azure Data Explorer в качестве источника данных для Kibana
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fac9c78607e50dca384670bf4cc08b50f723312b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065611"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Визуализация данных от Azure Data Explorer в Кибане с помощью разъема с открытым исходным кодом K2Bridge

K2Bridge (мост Кибана-Кусто) позволяет использовать Azure Data Explorer в качестве источника данных и визуализировать эти данные в Кибане. K2Bridge — это контейнерное приложение [с открытым исходным кодом,](https://github.com/microsoft/K2Bridge) которое действует как прокси-сервер между экземпляром Kibana и кластером Azure Data Explorer. В этой статье описывается, как использовать K2Bridge для создания этого соединения.

K2Bridge переводит запросы Kibana на язык киданных Kusto (КЗЛ) и отправляет результаты Azure Data Explorer обратно в Кибану. 

   ![Диаграмма](media/k2bridge/k2bridge-chart.png)

K2Bridge поддерживает вкладку Kibana Discover, где вы можете:
* Поиск и изучение данных
* Фильтрация результатов
* Добавление или удаление полей в сетке результатов
* Просмотр содержимого записи
* Сохранить и поделиться поиском

На рисунке ниже показан экземпляр Kibana, связанный с исследователем данных Azure By K2Bridge. Пользовательский опыт в Kibana остается неизменным.

   [![Страница Кибана](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Предварительные требования

Прежде чем вы сможете визуализировать данные из Azure Data Explorer в Кибане, имейте следующие готовые:

* [Helm V3](https://github.com/helm/helm#install), менеджер пакета Kubernetes
* Кластер Azure Kubernetes Service (AKS) или любой другой кластер Kubernetes (версия 1.14 к версии 1.16 была протестирована и проверена). Если вам нужен кластер AKS, переключите кластер AKS [с помощью Azure CLI](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) или [с помощью портала Azure](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
* [Кластер Azure Data Explorer,](create-cluster-database-portal.md)включающая:
    * URL-адрес кластера Azure Data Explorer 
    * Имя базы данных
    
* Директор службы Azure AD уполномочен просматривать данные в Azure Data Explorer, включая:
    * Идентификатор клиента 
    * Секрет клиента

    Рекомендуется директор службы с разрешением 'Viewer'. Не рекомендуется использовать более высокие разрешения.

    * [Установите разрешения на представление кластера для основного обслуживания Azure AD.](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)

    Для получения дополнительной информации о главном обслуживании Azure AD [см.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>Выполнить K2Bridge на Лазурный Kubernetes службы (AKS)

По умолчанию диаграмма K2Bridges's Helm ссылается на общедоступное изображение, размещенное в реестре контейнеров Корпорации Майкрософт (MCR). MCR не требует каких-либо учетных данных, и работает вне коробки.

1. Загрузите необходимые диаграммы Helm.

1. Добавьте зависимость Elasticsearch к шлему. 
    Причина зависимости elasticsearch заключается в том, что K2Bridge использует внутренний небольшой экземпляр Elasticsearch для обслуживания запросов, связанных с метаданными (например, шаблоны индексов и сохраненные запросы). В этом внутреннем экземпляре бизнес-данные не сохраняются, и их можно рассматривать как деталь реализации. 

    1. Чтобы добавить зависимость Elasticsearch к шлему:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. Чтобы получить диаграмму K2Bridge из-под каталога диаграмм репозитория GitHub:
        1. Клонировать репозиторий от [GitHub](https://github.com/microsoft/K2Bridge).
        1. Перейдите в каталог корневого репозитория K2Bridges.
        1. Выполните команду:

            ```bash
            helm dependency update charts/k2bridge
            ```

1. Развертывание K2Bridge:

    1. Установите переменные с правильными значениями для среды:

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. (Необязательно) Включите телеметрию Azure Application Insights. 
        Если это первый раз, когда вы используете Azure Application Insights, сначала следует [создать ресурс Application Insights.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) Вам нужно будет [скопировать ключ приборов](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) к переменной: 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>Установите диаграмму K2Bridge:

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        В [конфигурации](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) можно найти полный набор параметров конфигурации.

    1. Вывод командования предложит следующей команде Helm запустить для развертывания Kibana. Дополнительно, запустить:

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
        
    1. Используйте переправку порта для доступа к Кибане на localhost: 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
        
    1. Подключитесь к Кибане, просматривая http://127.0.0.1:5601.

    1. Разоблачить Kibana конечным пользователям. Есть несколько методов, чтобы сделать это. Метод, который вы используете во многом зависит от вашего использования случае.

        Пример:

        Экспонируйте услугу как услугу LoadBalancer. Для этого добавьте `--set service.type=LoadBalancer` параметр в команду установки K2Bridge Helm[(выше).](#install-k2bridge-chart)        
    
        Далее выполните:
        
        ```bash
        kubectl get service -w -n k2bridge
        ```
        
        Выходные данные должны выглядеть так: 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
 
        Затем вы можете использовать генерируемый EXTERNAL-IP, который появляется, и использовать `<EXTERNAL-IP>:5601`его для доступа к Kibana, открыв браузер для .

1. Настройка шаблонов индексов для доступа к данным:  
В новом экземпляре Кибаны:
     1. Откройте Кибана.
     1. Перейдите к управлению.
     1. Выберите **шаблоны индексов**. 
     1. Создайте шаблон индекса.
Название индекса должно точно соответствовать названию таблицы или имени функции без звездочки. Вы можете скопировать соответствующую строку из списка.

> [!Note]
> Чтобы работать на других поставщиков Kubernetes, измените `values.yaml` хранилище ElasticsearchClassName в соответствии с тем, который предлагается поставщиком.

## <a name="visualize-data"></a>Визуализируйте данные

Когда Azure Data Explorer настроен как источник данных для Kibana, можно использовать Kibana для изучения данных. 

1. В Кибане, в левом меню, выберите вкладку **Discover.**

1. Из левого списка выпадающих списков выберите шаблон индекса (в данном случае таблицу Explorer данных Azure), который определяет источник данных, который вы хотите изучить.
    
   ![Выберите шаблон индекса](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Если данные имеют поле фильтра времени, можно указать временной диапазон. В верхнем правом углу страницы установите фильтр времени. По умолчанию Discover показывает данные за последние 15 минут.

   ![Фильтр времени](media/k2bridge/k2bridge-time-filter.png)
    
1. Таблица результатов показывает первые 500 записей. Можно расширить документ для изучения данных полевых данных в форматах JSON или таблицы.

   ![Расширить запись](media/k2bridge/k2bridge-expand-record.png)

1. По умолчанию таблица результатов включает столбцы для _source документа и поля времени (если она существует). Можно выбрать определенные столбцы, которые будут добавлены в таблицу результатов, выбрав **добавить** рядом с именем поля в левой боковой панели.

   ![Конкретные столбцы](media/k2bridge/k2bridge-specific-columns.png)
    
1. В панели запросов можно искать данные по следующим образом:
    * Ввод термина поиска
    * Использование синтаксиса запроса Lucene. 
    Пример:
        * Поиск "ошибка", чтобы найти все записи, которые содержат это значение. 
        * Поиск "статус: 200", чтобы получить все записи со значением статуса 200. 
    * Использование логических операторов (И, ИЛИ, НЕ)
    * Использование подстановочных знаков \* (звездочка "или вопросительный знак "?") Например:
        * Запрос `"destination_city: L*"` будет соответствовать записям, где значение города назначения начинается с "l" (K2Bridge не является чувствительным к случаям).

    ![Выполнение запроса](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > В [поиске](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)вы можете найти больше правил поиска и логики.

1. Чтобы отфильтровать результаты поиска, используйте **список полей** на правой боковой панели страницы. 
    Список полей, где вы можете увидеть:
    * Пять лучших значений для поля
    * Количество записей, содержащих поле
    * Процент записей, содержащих каждое значение. 
    
    >[!Tip]
    > Используйте значок увеличификатора, чтобы найти все записи, которые имеют определенное значение.
    
    ![Список полей](media/k2bridge/k2bridge-field-list.png)
   
    Вы также можете отфильтровать результаты, используя значок увеличителя в представлении формата таблицы результатов каждой записи в таблице результатов.
    
     ![Список «Таблица»](media/k2bridge/k2bridge-table-list.png)
    
1. Выберите либо **сохранить** или **поделиться** своим поиском.

     ![Сохранить поиск](media/k2bridge/k2bridge-save-search.png)
