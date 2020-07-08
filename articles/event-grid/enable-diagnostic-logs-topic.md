---
title: Сетка событий Azure. Включение журналов диагностики для разделов или доменов
description: В этой статье приводятся пошаговые инструкции по включению журналов диагностики для службы "Сетка событий Azure".
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 06/10/2020
ms.author: spelluru
ms.openlocfilehash: 253d1fb933c32735f68cf6a2d471a7687caf5301
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84670174"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>Включение журналов диагностики для разделов и доменов службы "Сетка событий Azure"
Параметры диагностики позволяют пользователям службы "Сетка событий" записывать и просматривать журналы **сбоев публикации и доставки** в учетной записи хранения, концентраторе событий или рабочей области log Analytics. В этой статье приводятся пошаговые инструкции по включению этих параметров в сетку событий.

## <a name="prerequisites"></a>Предварительные условия

- Раздел подготовленной сетки событий
- Подготовленное назначение для записи журналов диагностики. Он может выполнять одно из следующих назначений в том же расположении, что и сетка событий:
    - Учетная запись хранения Azure.
    - концентратор событий;
    - Рабочая область Log Analytics

## <a name="enable-diagnostic-logs-for-a-custom-topic"></a>Включение журналов диагностики для пользовательского раздела

> [!NOTE]
> Следующая процедура содержит пошаговые инструкции по включению журналов диагностики для раздела. Действия по включению журналов диагностики для домена очень похожи. На шаге 2 перейдите к **домену** сетки событий в портал Azure.  

1. Войдите на [портал Azure](https://portal.azure.com).
2. Перейдите к разделу сетки событий, для которого необходимо включить параметры журнала диагностики. 
    1. На панели поиска в верхней части окна выполните поиск по **ссылке "Сетка событий**". 
    
        ![Поиск пользовательских разделов](./media/enable-diagnostic-logs-topic/search-custom-topics.png)
    1. Выберите **раздел** из списка, для которого необходимо настроить параметры диагностики. 
1. Выберите **параметры диагностики** в разделе **мониторинг** в меню слева.
1. На странице **параметры диагностики** выберите **Добавить новый параметр диагностики**. 
    
    ![Кнопка добавления параметра диагностики](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Укажите **имя** параметра диагностики. 
7. Выберите параметры **деливерифаилурес** и **Публишфаилурес** в разделе **Журнал** . 
    ![Выберите сбои](./media/enable-diagnostic-logs-topic/log-failures.png)
6. Включите одно или несколько назначений захвата для журналов, а затем настройте их, выбрав ранее созданный ресурс записи. 
    - Если вы выбрали **архивировать в учетную запись хранения**, выберите **учетная запись хранения — Настройка**, а затем выберите учетную запись хранения в подписке Azure. 

        ![Архивация в учетную запись хранения Azure](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Если выбрать **поток в концентраторе событий**, выберите **концентратор событий — настроить**, а затем — пространство имен концентраторов событий, концентратор событий и политика доступа. 
        ![Поток в концентратор событий](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Если вы выбрали **отправить log Analytics**, выберите рабочую область log Analytics.
        ![Отправить в Log Analytics.](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. Нажмите кнопку **Сохранить**. Затем выберите **X** в правом верхнем углу, чтобы закрыть страницу. 
9. Теперь вернитесь на страницу **параметры диагностики** и убедитесь, что в таблице **параметры диагностики** отображается новая запись. 
    ![Параметр диагностики в списке](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Вы также можете включить сбор всех метрик для раздела. 

## <a name="enable-diagnostic-logs-for-a-system-topic"></a>Включение журналов диагностики для системного раздела

1. Войдите на [портал Azure](https://portal.azure.com).
2. Перейдите к разделу сетки событий, для которого необходимо включить параметры журнала диагностики. 
    1. На панели поиска в верхней части окна выполните поиск по **системным темам службы "Сетка событий**". 
    
        ![Поиск системных разделов](./media/enable-diagnostic-logs-topic/search-system-topics.png)
    1. Выберите **системный раздел** , для которого требуется настроить параметры диагностики. 
    
        ![Выбор системного раздела](./media/enable-diagnostic-logs-topic/select-system-topic.png)
3. Выберите **параметры диагностики** в разделе **мониторинг** в меню слева и щелкните **Добавить параметр диагностики**. 

    ![Кнопка "добавить параметры диагностики"](./media/enable-diagnostic-logs-topic/system-topic-add-diagnostic-settings-button.png)
4. Укажите **имя** параметра диагностики. 
7. Выберите **деливерифаилурес** в разделе **Журнал** . 
    ![Выбор сбоев доставки](./media/enable-diagnostic-logs-topic/system-topic-select-delivery-failures.png)
6. Включите одно или несколько назначений захвата для журналов, а затем настройте их, выбрав ранее созданный ресурс записи. 
    - Если вы выбрали **отправить log Analytics**, выберите рабочую область log Analytics.
        ![Отправить в Log Analytics.](./media/enable-diagnostic-logs-topic/system-topic-select-log-workspace.png) 
    - Если вы выбрали **архивировать в учетную запись хранения**, выберите **учетная запись хранения — Настройка**, а затем выберите учетную запись хранения в подписке Azure. 

        ![Архивация в учетную запись хранения Azure](./media/enable-diagnostic-logs-topic/system-topic-select-storage-account.png)
    - Если выбрать **поток в концентраторе событий**, выберите **концентратор событий — настроить**, а затем — пространство имен концентраторов событий, концентратор событий и политика доступа. 
        ![Поток в концентратор событий](./media/enable-diagnostic-logs-topic/system-topic-select-event-hub.png)
8. Нажмите кнопку **Сохранить**. Затем выберите **X** в правом верхнем углу, чтобы закрыть страницу. 
9. Теперь вернитесь на страницу **параметры диагностики** и убедитесь, что в таблице **параметры диагностики** отображается новая запись. 
    ![Параметр диагностики в списке](./media/enable-diagnostic-logs-topic/system-topic-diagnostic-settings-targets.png)

     Вы также можете включить сбор всех **метрик** для системного раздела.

    ![Системный раздел — Включение всех метрик](./media/enable-diagnostic-logs-topic/system-topics-metrics.png)

## <a name="view-diagnostic-logs-in-azure-storage"></a>Просмотр журналов диагностики в службе хранилища Azure 

1. Когда вы включите учетную запись хранения в качестве назначения для записи и начнете создавать журналы диагностики, в учетной записи хранения должны отобразиться новые контейнеры с именем **Insights-Logs-деливерифаилурес** и **Insights-Logs-публишфаилурес** . 

    ![Хранилище — контейнеры для журналов диагностики](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. При переходе по одному из контейнеров вы получите большой двоичный объект в формате JSON. Файл содержит записи журнала для сбоя доставки или ошибки публикации. Путь перехода представляет идентификатор **ResourceId** раздела сетки событий и отметку времени (уровень "минута") на момент выдачи записей журнала. Файл большого двоичного объекта или JSON, который можно скачать, в конце соответствует схеме, описанной в следующем разделе. 

    [![Файл JSON в хранилище ](./media/enable-diagnostic-logs-topic/select-json.png)](./media/enable-diagnostic-logs-topic/select-json.png)
3. Вы должны увидеть содержимое в файле JSON, как показано в следующем примере: 

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, systemId=17284f7c-0044-46fb-84b7-59fda5776017, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```

## <a name="next-steps"></a>Дальнейшие шаги
Сведения о схеме журнала и других концептуальных данных о журналах диагностики для разделов или доменов см. в разделе [журналы диагностики](diagnostic-logs.md).
