---
title: Используйте Application Insights для мониторинга службы Azure Kubernetes (AKS) или других размещенных в Kubernetes приложений (Azure Monitor | Документация Майкрософт
description: Azure Monitor использует технологию сетчатой службы, Istio, в кластере Kubernetes для обеспечения мониторинга приложений для любого размещенного в Kubernetes приложения. Это позволяет выполнять сбор данных телеметрии Application Insights, относящихся к входящим и исходящим запросам из модулей Pod, выполняющихся в кластере.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 3056b6c56be32cf5c054c4526a88157650a3e30b
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820778"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>Мониторинг приложений с нулевым инструментированием для приложений, размещенных в Kubernetes

> [!IMPORTANT]
> В настоящее время эта функция доступна в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Monitor теперь использует службу "сеть за пределами службы" в кластере Kubernetes, чтобы обеспечить поддержку мониторинга приложений для любого приложения Kubernetes. С помощью стандартных функций Application Insights, таких как [схема приложений](../../azure-monitor/app/app-map.md) , для моделирования зависимостей, [Live Metrics Stream](../../azure-monitor/app/live-stream.md) для мониторинга в режиме реального времени, мощных визуализаций с помощью [панели мониторинга по умолчанию](../../azure-monitor/app/overview-dashboard.md), [обозревателя метрик](../../azure-monitor/platform/metrics-getting-started.md)и [ Книги](../../azure-monitor/app/usage-workbooks.md). Эта функция поможет пользователям выявить узкие места производительности и точки отказа во всех рабочих нагрузках Kubernetes в выбранном пространстве имен Kubernetes. При использовании существующих инвестиций в сеть служб с помощью таких технологий, как Istio, Azure Monitor обеспечивает автоматическое отслеживание приложений без каких-либо изменений в коде приложения.

> [!NOTE]
> Это один из многих способов выполнения мониторинга приложений на Kubernetes. Вы также можете инструментировать любое приложение, размещенное в Kubernetes, с помощью [пакета SDK для Application Insights](../../azure-monitor/azure-monitor-app-hub.md) , не требуя наличия сетки службы. Для мониторинга Kubernetes без инструментирования приложения с помощью пакета SDK можно использовать приведенный ниже метод.

## <a name="prerequisites"></a>Технические условия

- [Кластер Kubernetes](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Доступ консоли к кластеру для запуска *kubectl*.
- [Ресурс Application Insights](create-new-resource.md) ;
- Наличие сетки службы. Если в кластере не развернут Istio, вы можете узнать, как [установить и использовать Istio в службе Kubernetes Azure](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Возможности

Используя функцию мониторинга приложений с нулевым инструментированием для размещенных приложений Kubernetes, вы сможете использовать:

- [Схема сопоставления приложений](../../azure-monitor/app/app-map.md)
- [Метрики Live Stream](../../azure-monitor/app/live-stream.md)
- [Панели мониторинга](../../azure-monitor/app/overview-dashboard.md)
- [обозреватель метрик](../../azure-monitor/platform/metrics-getting-started.md)
- [Распределенная трассировка](../../azure-monitor/app/distributed-tracing.md)
- [Сквозное отслеживание транзакций](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Шаги установки

Чтобы включить решение, мы будем выполнять следующие действия:
- Разверните приложение (если оно еще не развернуто).
- Убедитесь, что приложение является частью сети службы.
- Наблюдение за сбором данных телеметрии.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Настройка приложения для работы с сеткой служб

Istio поддерживает два способа [инструментирования Pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
В большинстве случаев проще всего пометить пространство имен Kubernetes, содержащее приложение, меткой *istio-Injection* :

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Так как сетка службы перерывает данные за пределами сети, мы не можем перехватить зашифрованный трафик. Для трафика, который не оставляет кластер, используйте незашифрованный протокол (например, HTTP). Для внешнего трафика, который необходимо зашифровать, рассмотрите возможность [настройки завершения SSL](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) на контроллере входящих подключений.

Приложения, работающие за пределами сети службы, не затрагиваются.

### <a name="deploy-your-application"></a>Развертывание приложения

- Разверните приложение в пространстве имен *My-App-Namespace* . Если приложение уже развернуто и вы выполнили описанный выше метод автоматического внедрения расширения, необходимо повторно создать модули Pod, чтобы гарантировать, что Istio внедряет свой расширения. либо инициируйте последовательное обновление, либо удалите отдельные модули Pod и дождитесь их повторного создания.
- Убедитесь, что приложение соответствует [требованиям Istio](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Развертывание мониторинга приложений с нулевым инструментированием для размещенных приложений Kubernetes

1. Скачайте и извлеките [ *Application Insightsный выпуск адаптера* ](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Перейдите к */СРК/кубернетес/* в папке Release.
3. Изменение *Application-Insights-istio-микшер-Adapter-Deployment. YAML*
    - Измените значение переменной среды *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* , чтобы она содержала ключ инструментирования ресурса Application Insights в портал Azure для хранения данных телеметрии.
    - При необходимости измените значение переменной среды *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* , чтобы оно содержало разделенный запятыми список пространств имен, для которых необходимо включить мониторинг. Оставьте поле пустым, чтобы отслеживать все пространства имен.
4. Примените *все* файлы YAML, найденные в разделе *src/kubernetes/* , выполнив следующую команду (по-прежнему необходимо находиться внутри */СРК/кубернетес/* ):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Проверка развертывания

- Убедитесь, что Application Insights адаптер был развернут:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> В некоторых случаях требуется точная настройка. Чтобы включить или исключить данные телеметрии для отдельного модуля из сбора, используйте метку *appinsights/Monitoring. Enabled* для этого Pod. Это будет иметь приоритет над всей конфигурацией на основе пространства имен. Задайте для параметра *appinsights/Monitor. Enabled* *значение true* , чтобы включить Pod, и *значение false* , чтобы исключить его.

### <a name="view-application-insights-telemetry"></a>Просмотр данных телеметрии Application Insights

- Создайте пример запроса к приложению, чтобы убедиться, что мониторинг работает правильно.
- В течение 3-5 минут следует начать просмотр данных телеметрии в портал Azure. Обязательно ознакомьтесь с разделом *схема приложений* на Application Insights ресурсе на портале.

## <a name="troubleshooting"></a>Устранение неисправностей

Ниже приведена последовательность устранения неполадок, которая используется, когда данные телеметрии не отображаются в портал Azure, как ожидалось.

1. Убедитесь, что приложение находится под загрузкой и отправляет и получает запросы в виде обычного HTTP. Так как данные телеметрии превышены, зашифрованный трафик не поддерживается. Если входящие или исходящие запросы отсутствуют, данные телеметрии не будут отображаться.
2. Убедитесь, что в переменной среды *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* в *Application-Insights-ISTIO-микшер-Adapter-Deployment. YAML*указан правильный ключ инструментирования. Ключ инструментирования находится на вкладке *Обзор* ресурса Application Insights в портал Azure.
3. Убедитесь, что в переменной среды *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* в *Application-Insights-ISTIO-микшер-Adapter-Deployment. YAML*указано правильное пространство имен Kubernetes. Оставьте поле пустым, чтобы отслеживать все пространства имен.
4. Убедитесь, что модули расширения приложения были добавлены Istio. Убедитесь, что расширения Istio существует для каждого Pod.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Убедитесь в наличии контейнера с именем *istio-proxy* , выполняющегося в модуле Pod.

5. Просмотр трассировок Application Insights адаптера.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Число полученных элементов телеметрии обновляется один раз в минуту. Если оно не растет в минуту, данные телеметрии не отправляются на адаптер Istio.
   Найдите ошибки в журнале.
6. Если вы установили, что *Application Insights для адаптера Kubernetes* не ведет телеметрию, проверьте журналы микшера Istio, чтобы выяснить, почему не отправляет данные на адаптер:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Найдите ошибки, особенно относящиеся к связи с адаптером *аппликатионинсигхтсадаптер* .

## <a name="faq"></a>Вопросы и ответы

Последние сведения о ходе выполнения этого проекта см. в [Application Insights адаптере GitHub для проекта Istio микшера](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Удаление

Чтобы удалить продукт, для *каждого* файла YAML, найденного в разделе *src/kubernetes/* Run:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о совместной работе Azure Monitor и контейнеров см. в статье [обзор Azure Monitor для контейнеров](../../azure-monitor/insights/container-insights-overview.md) .