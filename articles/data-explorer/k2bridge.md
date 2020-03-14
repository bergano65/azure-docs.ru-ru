---
title: Визуализация данных из Azure обозреватель данных с помощью Kibana
description: Из этой статьи вы узнаете, как настроить обозреватель данных Azure в качестве источника данных для Kibana.
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 30d74f36c6462d1fba039595d2ed6fe722b742e8
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79164817"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Визуализация данных из Azure обозреватель данных в Kibana с помощью соединителя K2Bridge с открытым кодом

K2Bridge (Kibana-Kusto Bridge) позволяет использовать обозреватель данных Azure в качестве источника данных и визуализировать эти данные в Kibana. K2Bridge — это контейнерное приложение с [открытым кодом](https://github.com/microsoft/K2Bridge) , которое выступает в качестве прокси-сервера между экземпляром Kibana и кластером Azure обозреватель данных. В этой статье описывается, как создать это подключение с помощью K2Bridge.

K2Bridge преобразует запросы Kibana к языку запросов Kusto (ККЛ) и отправляет результаты обозреватель данных Azure обратно в Kibana. 

   ![Диаграмма](media/k2bridge/k2bridge-chart.png)

K2Bridge поддерживает вкладку обнаружения Kibana, где можно:
* Поиск и просмотр данных
* Фильтрация результатов
* Добавление или удаление полей в сетке результатов
* Просмотр содержимого записи
* Сохранение и совместное использование поисков

На рисунке ниже показан экземпляр Kibana, привязанный к Azure обозреватель данных by K2Bridge. Взаимодействие с пользователем в Kibana не изменилось.

   [Страница ![Kibana](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>предварительные требования

Прежде чем можно будет визуализировать данные из Azure обозреватель данных в Kibana, выполните следующие действия:

* [Helm v3](https://github.com/helm/helm#install), диспетчер пакетов Kubernetes
* Кластер Azure Kubernetes Service (AKS) или любой другой кластер Kubernetes (версии 1,14 до версии 1,16 были протестированы и проверены). Если вам нужен кластер AKS, см. раздел Развертывание кластера AKS [с помощью Azure CLI](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) или [с помощью портал Azure](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
* [Кластер Azure обозреватель данных](create-cluster-database-portal.md), в том числе:
    * URL-адрес кластера Azure обозреватель данных 
    * Имя базы данных
    
* Субъект-служба Azure AD, который имеет право просматривать данные в обозреватель данных Azure, в том числе:
    * Идентификатор клиента 
    * Секрет клиента

    Рекомендуется использовать субъект-службу с разрешением "средство просмотра". Не рекомендуется использовать более высокие разрешения.

    * [Задайте разрешения на просмотр кластера для субъекта-службы Azure AD](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

    Дополнительные сведения о субъекте-службе Azure AD см. в статье [Создание субъекта-службы Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>Запуск K2Bridge в службе Kubernetes Azure (AKS)

По умолчанию K2Bridges's Helm диаграмма ссылается на общедоступный образ, расположенный в реестре контейнеров Майкрософт (мкр). МКР не требует никаких учетных данных и работает готовым вариантом.

1. Скачайте необходимые диаграммы Helm.

1. Добавьте зависимость Elasticsearch в Helm. 
    Причина зависимости Elasticsearch заключается в том, что K2Bridge использует внутренний экземпляр Small Elasticsearch для обслуживания запросов, связанных с метаданными (например, индексы и сохраненные запросы). В этом внутреннем экземпляре бизнес-данные не сохраняются, и их можно рассматривать как подробности реализации. 

    1. Чтобы добавить зависимость Elasticsearch в Helm, сделайте следующее:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. Чтобы получить диаграмму K2Bridge из каталога Charts репозитория GitHub, сделайте следующее:
        1. Клонировать репозиторий из [GitHub](https://github.com/microsoft/K2Bridge).
        1. Перейдите в каталог корневого репозитория K2Bridges.
        1. Выполните команду:

            ```bash
            helm dependency update charts/k2bridge
            ```

1. Развертывание K2Bridge:

    1. Задайте переменные с правильными значениями для вашей среды:

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. Используемых Включение телеметрии Azure Application Insights. 
        Если вы используете Azure Application Insights в первый раз, сначала [Создайте ресурс Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource). Необходимо [скопировать ключ инструментирования](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) в переменную: 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>Установите диаграмму K2Bridge:

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        В [конфигурации](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) можно найти полный набор параметров конфигурации.

    1. В выходных данных команды будет предложено выполнить следующую команду Helm для выполнения развертывания Kibana. При необходимости выполните команду:

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
    1. Используйте перенаправление портов для доступа к Kibana на localhost: 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
    1. Подключитесь к Kibana, перейдя по адресу http://127.0.0.1:5601.

    1. Предоставление Kibana пользователям. Это можно сделать несколькими способами. Метод, который вы используете во многом, зависит от вашего варианта использования.

        Пример:

        Предоставление службы в качестве службы балансировки нагрузки. Для этого добавьте следующий параметр в команду K2Bridge Helm Install ([выше](#install-k2bridge-chart)):

        `--set service.type=LoadBalancer`
    
        Далее выполните:

           ```bash
           kubectl get service -w -n k2bridge
           ```   
        Выходные данные должны выглядеть так: 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
        Затем можно использовать созданный внешний IP-адрес и использовать его для доступа к Kibana, открыв браузер в: `\<EXTERNAL-IP>:5601`.

1. Настройте шаблоны индексов для доступа к данным:  
В новом экземпляре Kibana:
     1. Откройте Kibana.
     1. Перейдите в раздел Управление.
     1. Выберите **шаблоны индексов**. 
     1. Создание шаблона индекса.
Имя индекса должно точно совпадать с именем таблицы или функции без звездочки. Вы можете скопировать соответствующую строку из списка.

> [!Note]
> Чтобы запустить на других поставщиках Kubernetes, измените Elasticsearch Сторажекласснаме в `values.yaml` в соответствии с предложенным поставщиком.

## <a name="visualize-data"></a>Визуализируйте данные

Когда обозреватель данных Azure настроен в качестве источника данных для Kibana, можно использовать Kibana для просмотра данных. 

1. В Kibana в меню слева выберите вкладку **Обнаружение** .

1. В раскрывающемся списке слева выберите шаблон индекса (в нашем примере это таблица Azure обозреватель данных), определяющий источник данных, который необходимо исследовать.
    
   ![Выбор шаблона индекса](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Если в данных есть поле фильтра времени, можно указать диапазон времени. В верхнем правом углу страницы задайте фильтр времени. По умолчанию в окне Обнаружение отображаются данные за последние 15 минут.

   ![Фильтр времени](media/k2bridge/k2bridge-time-filter.png)
    
1. В таблице результатов показаны первые 500 записей. Можно развернуть документ, чтобы проверить данные его полей в форматах JSON или Table.

   ![Развернуть запись](media/k2bridge/k2bridge-expand-record.png)

1. По умолчанию таблица результатов содержит столбцы для документа _source и поле времени (если оно существует). Можно выбрать определенные столбцы для добавления в таблицу результатов, нажав кнопку **Добавить** рядом с именем поля в левой боковой панели.

   ![Определенные столбцы](media/k2bridge/k2bridge-specific-columns.png)
    
1. В строке запроса можно выполнять поиск по следующим данным:
    * Ввод условия поиска
    * Использование синтаксиса запроса Lucene. 
    Пример:
        * Выполните поиск по слову "ошибка", чтобы найти все записи, содержащие это значение. 
        * Выполните поиск по запросу "Status: 200", чтобы получить все записи со значением состояния 200. 
    * Использование логических операторов (AND или NOT)
    * Использование подстановочных знаков (звездочка "\*" или вопросительный знак "?") Например:
        * `"destination_city: L*"` запроса будет сопоставлять записи, в которых значение города назначения начинается с "l" (K2Bridge не учитывает регистр).

    ![Выполнение запроса](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > При [поиске](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)можно найти дополнительные правила и логику поиска.

1. Чтобы отфильтровать результаты поиска, используйте **список полей** в правой боковой панели страницы. 
    В списке полей можно увидеть следующее:
    * Первые пять значений для поля
    * Число записей, содержащих поле
    * Процент записей, содержащих каждое значение. 
    
    >[!Tip]
    > Используйте значок лупы (+), чтобы найти все записи, имеющие определенное значение.
    
    ![Список полей](media/k2bridge/k2bridge-field-list.png)
   
    Можно также отфильтровать результаты с помощью значка лупы (+) в представлении формата таблицы результатов для каждой записи в таблице результатов.
    
     ![Список «Таблица»](media/k2bridge/k2bridge-table-list.png)
    
1. Выберите вариант, чтобы **сохранить** или **поделиться** поиском.

     ![Сохранить поиск](media/k2bridge/k2bridge-save-search.png)
