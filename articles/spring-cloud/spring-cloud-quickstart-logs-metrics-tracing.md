---
title: Краткое руководство. Мониторинг приложений Azure Spring Cloud с помощью журналов, метрик и трассировки
description: Используйте потоковую передачу журналов, анализ журналов, метрики и трассировку для мониторинга примеров приложений PiggyMetrics в Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: f9f03c355e1e619d004c8ec8c1cc2f91932db744
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046839"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Краткое руководство. Мониторинг приложений Azure Spring Cloud с помощью журналов, метрик и трассировки

Встроенные возможности мониторинга Azure Spring Cloud позволяют легко выполнять отладку и мониторинг при возникновении сложных проблем. Azure Spring Cloud интегрирует [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) с Azure [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Эта интеграция предоставляет широкие возможности ведения журналов, анализа метрик и распределенной трассировки с помощью портала Azure. В следующих процедурах объясняется, как использовать потоковую передачу журналов, анализ журналов, метрики и распределенную трассировку с развернутыми приложениями PiggyMetrics.

## <a name="prerequisites"></a>Предварительные требования

Выполните предыдущие шаги. 

* [Подготовка к работе экземпляра Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md)
* [Настройка сервера конфигурации](spring-cloud-quickstart-setup-config-server.md)
* [Сборка и развертывание приложений](spring-cloud-quickstart-deploy-apps.md)

## <a name="logs"></a>Журналы

Журналы в Azure Spring Cloud можно просматривать двумя способами: с помощью **потоковой передачи журналов** в режиме реального времени для каждого экземпляра приложения или **анализа журналов** для агрегированных журналов с расширенными возможностями запросов.

### <a name="log-streaming"></a>Потоковая передача журналов

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Потоковую передачу журнала можно задействовать в Azure CLI с помощью следующей команды.

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

Журналы отобразятся в следующем виде:

[ ![Потоковая передача журналов из Azure CLI](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> Используйте `az spring-cloud app logs -h` для просмотра дополнительных параметров и функциональных возможностей потоковой передачи журналов.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Чтобы получить журналы с помощью Azure Toolkit for IntelliJ, выполните следующие действия:

1. Выберите **Azure Explorer** и **Spring Cloud**.

1. Щелкните правой кнопкой мыши запущенное приложение.

1. Выберите **Streaming Logs** (Журналы потоковой передачи) из раскрывающегося списка.

   ![Выбор журналов потоковой передачи](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. Выберите **Экземпляр**.

   ![Выбор экземпляра](media/spring-cloud-intellij-howto/select-instance.png)
    
1. Журнал потоковой передачи отобразится в окне выходных данных.

   ![Вывод журнала потоковой передачи](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. Перейдите на страницу **служба | Общие сведения** и выберите **Журналы** в разделе **Мониторинг**. Щелкните **Выполнить** для одного из примеров запросов Azure Spring Cloud. 

   [ ![Запись анализа журналов](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Затем отобразятся отфильтрованные журналы. Дополнительные рекомендации по написанию запросов см. в [документации Azure Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries).

   [ ![Добавление запроса на анализ журналов](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>Метрики

1. Перейдите на страницу **служба | Общие сведения** и выберите **Метрики** в разделе **Мониторинг**. Добавьте свою первую метрику, выбрав значение `system.cpu.usage` для параметра **Метрика** и `Avg` для параметра **Агрегирование**, чтобы увидеть временную шкалу для общей загрузки ЦП.

   [ ![Ввод данных для метрики](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. На панели инструментов сверху щелкните **Добавить фильтр**, выберите `App=Gateway`, чтобы увидеть загрузку ЦП только для приложения **gateway**.

   [ ![Использование фильтра в разделе "Метрики"](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. Закройте созданный ранее фильтр, нажмите **Apply Splitting** (Применить разделение) и выберите `App` в поле **Значения**, чтобы увидеть загрузку ЦП, создаваемую различными приложениями.

   [ ![Применение разделения для метрик](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>Распределенная трассировка

1. Перейдите на страницу **служба | Общие сведения** и выберите **Distributed tracing** (Распределенная трассировка) в разделе **Мониторинг**. Затем перейдите на вкладку **View application map** (Просмотр схемы приложения) справа.

   [ ![Ввод данных для распределенной трассировки](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Теперь вы можете видеть состояние вызовов между приложениями PiggyMetrics. 

   [ ![Обзор распределенной трассировки](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. Щелкните ссылку между **gateway** и **account-service**, чтобы просмотреть дополнительные сведения, например самые медленные вызовы методов HTTP.

   [ ![Распределенная трассировка](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. Наконец, щелкните **Анализ работы**, чтобы воспользоваться расширенными возможностями встроенных средств анализа производительности.

   [ ![Распределенная трассировка производительности](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

## <a name="clean-up-resources"></a>Очистка ресурсов

На предыдущем шаге вы создали ресурсы Azure в группе ресурсов. Если эти ресурсы вам не понадобятся в будущем, вы можете удалить группу ресурсов из портала, или выполнив приведенную ниже команду в Cloud Shell.

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

При выполнении предыдущих шагов вы также задали имя группы ресурсов по умолчанию. Чтобы очистить поля от заданных по умолчанию значений, воспользуйтесь следующей командой в Cloud Shell:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о готовых к использованию возможностях мониторинга Azure Spring Cloud см. в следующих статьях:

> [!div class="nextstepaction"]
> [Службы диагностики](diagnostic-services.md)
> [Распределенная трассировка](spring-cloud-tutorial-distributed-tracing.md)
> [Потоковая передача журналов в режиме реального времени](spring-cloud-howto-log-streaming.md)
