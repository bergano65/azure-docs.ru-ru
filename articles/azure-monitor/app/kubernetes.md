---
title: Использование Application Insights для мониторинга Службы Azure Kubernetes (AKS) или других размещенных в Kubernetes приложений (Azure Monitor) | Документация Майкрософт
description: Azure Monitor использует технологию сетки служб Istio для кластера Kubernetes, чтобы поддерживать мониторинг для любого размещенного в Kubernetes приложения. Это позволяет собирать данные телеметрии Application Insights, имеющие отношение к входящим и исходящим запросам модулей pod, которые выполняются в кластере.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: a5e73039db541023b1fd4a9b75e7c14030c8e219
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797887"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications-with-istio---deprecated"></a>Мониторинг приложений, размещенных в Kubernetes, без инструментирования с помощью Istio (НЕРЕКОМЕНДУЕМЫЙ МЕТОД)

> [!IMPORTANT]
> Эта функциональность предоставляется в режиме нерекомендуемой, а после 1 августа 2020 года ее поддержка прекратится.
> В настоящее время мониторинг без кода можно включить только для [Java через автономный агент](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent). Для мониторинга приложений в AKS на других языках используйте пакеты средств разработки: [ASP.Net Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core), [ASP.Net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net), [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs), [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript) и [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

Azure Monitor теперь использует технологию сетки служб Istio для кластера Kubernetes, что позволяет без дополнительной настройки поддерживать мониторинг для любого размещенного в Kubernetes приложения. Вы можете использовать стандартные возможности Application Insights, такие как [схема приложений](../../azure-monitor/app/app-map.md) для моделирования зависимостей, [Live Metrics Stream](../../azure-monitor/app/live-stream.md) для мониторинга в режиме реального времени, мощные визуализации на [панели мониторинга](../../azure-monitor/app/overview-dashboard.md), [Обозреватель метрик](../../azure-monitor/platform/metrics-getting-started.md)и [Книги](../../azure-monitor/platform/workbooks-overview.md). Эта возможность поможет пользователям выявлять узкие места производительности и точки вероятного отказа для всех рабочих нагрузок Kubernetes, размещенных в выбранном пространстве имен Kubernetes. Используя возможности имеющихся сеток с служб и совмещая их с такими решениями, как Istio, служба Azure Monitor способна поддерживать мониторинг приложений с автоматическим инструментированием, не внося никаких изменений в код приложения.

> [!NOTE]
> Это один из многих способов выполнять мониторинг приложений в Kubernetes. Вы также можете инструментировать любое размещенное в Kubernetes приложение с помощью [пакета SDK для Application Insights ](../../azure-monitor/azure-monitor-app-hub.yml) даже без сетки служб. Чтобы организовать мониторинг в Kubernetes без инструментирования приложения с помощью пакета SDK, попробуйте использовать приведенный ниже метод.

## <a name="prerequisites"></a>Предварительные требования

- [Кластер Kubernetes](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Доступ из консоли к кластеру, чтобы выполнить *kubectl*.
- [Ресурс Application Insights](create-new-resource.md).
- Наличие сетки служб. Если в кластере не установлена платформа Istio, изучите статью [Установка и использование Istio в Службе Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Возможности

Используя мониторинг без инструментирования для приложений, размещенных в Kubernetes, вы сможете применить следующие компоненты.

- [Схема сопоставления приложений](../../azure-monitor/app/app-map.md)
- [Live Metrics Stream](../../azure-monitor/app/live-stream.md)
- [Панели мониторинга](../../azure-monitor/app/overview-dashboard.md)
- [Обозреватель метрик](../../azure-monitor/platform/metrics-getting-started.md)
- [Распределенная трассировка](../../azure-monitor/app/distributed-tracing.md)
- [Сквозной мониторинг транзакций](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Шаги установки

Чтобы включить решение, мы выполним следующие действия.
- Развертывание приложения (если оно еще не развернуто).
- Проверка включения приложения в сетку служб.
- Отслеживание собираемой телеметрии.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Настройка приложения для работы с сеткой служб.

Istio поддерживает два способа [инструментирования pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
В большинстве случаев проще всего присвоить метку *istio-injection* пространству имен Kubernetes, которое содержит приложение:

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Так как сетка служб отправляет данные за пределы сети, мы не можем перехватить зашифрованный трафик. Для трафика, который не покидает кластер, используйте протокол без шифрования (например, HTTP). Для внешнего трафика, который должен быть шифрованным, попробуйте [настроить завершение TLS](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) на контроллере входящих подключений.

Это никак не повлияет на работу приложений, работающих за пределами сетки служб.

### <a name="deploy-your-application"></a>Развертывание приложения

- Разверните приложение в пространство имен *my-app-namespace*. Если это приложение уже развернуто и вы реализовали описанный выше метод автоматического внедрения, необходимо повторно создать pod, чтобы Istio внедрила в них свои расширения. Для этого запустите последовательное обновление либо по одному удаляйте pod и ожидайте их повторного создания.
- Убедитесь, что приложение соответствует [требованиям для Istio](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Развертывание мониторинга без инструментирования для приложений, размещенных в Kubernetes

1. Скачайте и извлеките [выпуск *адаптера Application Insights*](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Перейдите к папке */src/kubernetes/* в папке выпуска.
3. Измените файл *application-insights-istio-mixer-adapter-deployment.yaml*,
    - чтобы переменная среды *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* содержала ключ инструментирования для ресурса Application Insights, где будут храниться данные телеметрии.
    - При необходимости измените значение переменной среды *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES*, чтобы она содержала разделенный запятыми список пространств имен, для которых требуется включить мониторинг. Чтобы выполнялся мониторинг всех пространств имен, оставьте это поле пустым.
4. Примените *каждый* YAML-файл в папке *src/kubernetes/* , выполнив следующую команду (папка */src/kubernetes/* должна оставаться текущей):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Проверка развертывания

- Убедитесь, что успешно развернут адаптер Application Insights:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> В некоторых случаях потребуется дополнительная настройка. Чтобы включить или отключить сбор телеметрии для отдельного pod, присвойте ему метку *appinsights/monitoring.enabled*. Эта метка имеет более высокий приоритет, чем все конфигурации на основе пространств имен. Задайте для параметра *appinsights/monitoring.enabled* значение *true*, чтобы добавить pod, или *false*, чтобы исключить.

### <a name="view-application-insights-telemetry"></a>Просмотр телеметрии Application Insights

- Создайте пример запроса к приложению, чтобы проверить правильность работы мониторинга.
- Примерно через 3–5 минут вы увидите, что данные телеметрии появляются на портале Azure. Обязательно проверьте на портале Azure раздел *Схема приложений* для соответствующего ресурса Application Insights.

## <a name="troubleshooting"></a>Устранение неполадок

Ниже приведен алгоритм устранения неполадок на случай, если данные телеметрии не отображаются на портале Azure правильным образом.

1. Убедитесь, что приложение запущено и успешно отправляет и получает запросы по протоколу HTTP. Так как данные телеметрии передаются за пределы сети, зашифрованный трафик не отслеживается. Если входящие или исходящие запросы отсутствуют, данные телеметрии не будут отображаться.
2. Убедитесь, что в переменной среды *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* в файле *application-insights-istio-mixer-adapter-deployment.yaml* указан правильный ключ инструментирования. Скопируйте ключ инструментирования на странице *общих сведений* для ресурса Application Insights на портале Azure.
3. Убедитесь, что указано правильное пространство имен Kubernetes в переменной среды *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* в файле *application-insights-istio-mixer-adapter-deployment.yaml*. Чтобы выполнялся мониторинг всех пространств имен, оставьте это поле пустым.
4. Убедитесь, что в pod для приложения добавлены расширения Istio. Убедитесь, что расширение Istio существует для каждого pod.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Убедитесь, что в pod выполняется контейнер с именем *istio-proxy*.

5. Просмотрите трассировку адаптера Application Insights.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Число полученных элементов телеметрии обновляется один раз в минуту. Если оно не увеличивается ежеминутно, значит Istio не отправляет данные телеметрии в адаптер.
   Проверьте файл журнала на наличие ошибок.
6. Если вы установили, что адаптер *Application Insights для Kubernetes* не получает телеметрию, проверьте журналы Istio Mixer и разберитесь, почему не отправляются данные в адаптер.

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Ищите любые ошибки, особенно относящиеся к коммуникации с адаптером *applicationinsightsadapter*.

## <a name="faq"></a>ВОПРОСЫ И ОТВЕТЫ

Для получения последних сведений о ходе выполнения этого проекта посетите [репозиторий проекта адаптера Application Insights для Istio Mixer](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq) на сайте GitHub.

## <a name="uninstall"></a>Удаление

Чтобы удалить продукт, выполните для *каждого* YAML-файла в папке *src/kubernetes/* следующую команду:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о совместной работе Azure Monitor и контейнеров см. в статье [Обзор службы "Azure Monitor для контейнеров"](../../azure-monitor/insights/container-insights-overview.md).
