---
title: Используйте приложения Insights для мониторинга службы Azure Kubernetes (AKS) или других размещенных приложений Kubernetes - Azure Monitor Документы Майкрософт
description: Azure Monitor использует технологию сервисной сетки Istio в вашем кластере Kubernetes для мониторинга приложений для любого размещенного приложения Kubernetes. Это позволяет собирать телеметрию Application Insights, относящуюся к входящим и исходящим запросам в и из стручков, работающих в вашем кластере.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 56a0cb66f5b54c817067970ab369d7ca471a1696
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132342"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>Нулевой мониторинг приложений приборов для Kubernetes размещаемых приложений

> [!IMPORTANT]
> Эта функциональность в настоящее время находится в общедоступном предварительном просмотре.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Monitor теперь использует технологию сетки служб ы в вашем кластере Kubernetes, чтобы обеспечить мониторинг приложения коробки для любого приложения, размещенного в Kubernetes. С функциями Application Insight по умолчанию, такими как [Application Map](../../azure-monitor/app/app-map.md) для моделирования зависимостей, [Live Metrics Stream](../../azure-monitor/app/live-stream.md) для мониторинга в реальном времени, мощные визуализации с [панелью мониторинга по умолчанию,](../../azure-monitor/app/overview-dashboard.md) [Metric Explorer](../../azure-monitor/platform/metrics-getting-started.md)и [Workbooks.](../../azure-monitor/app/usage-workbooks.md) Эта функция поможет пользователям определить узкие места производительности и точки доступа сбоев во всех своих рабочих нагрузках Kubernetes в выбранном пространстве имен Kubernetes. Воспользовавшись существующими инвестициями в сервисную сетку с помощью таких технологий, как Istio, Azure Monitor позволяет осуществлять автоматическое мониторинг приложений без каких-либо изменений в коде приложения.

> [!NOTE]
> Это один из многих способов выполнения мониторинга приложений на Kubernetes.Вы также можете сделать инструмент любое приложение, размещенное в Kubernetes, используя [Приложение Insights SDK](../../azure-monitor/azure-monitor-app-hub.yml) без необходимости в сервисной сетке. Для мониторинга Kubernetes без инструментирования приложения с Помощью SDK вы можете использовать ниже метод.

## <a name="prerequisites"></a>Предварительные требования

- [Кластер Kubernetes](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Консольный доступ к кластеру для запуска *kubectl*.
- [Ресурс Insight приложений](create-new-resource.md)
- Иметь сервисную сетку. Если в кластере нет развертывания Istio, вы можете узнать, как [установить и использовать Istio в службе Azure Kubernetes.](https://docs.microsoft.com/azure/aks/istio-install)

## <a name="capabilities"></a>Возможности

С помощью нулевого мониторинга приложений приборов для размещенных приложений Kubernetes вы сможете использовать:

- [Схема сопоставления приложений](../../azure-monitor/app/app-map.md)
- [Прямая метрика](../../azure-monitor/app/live-stream.md)
- [Панели мониторинга](../../azure-monitor/app/overview-dashboard.md)
- [Исследователь метрик](../../azure-monitor/platform/metrics-getting-started.md)
- [Распределенный отслеживание](../../azure-monitor/app/distributed-tracing.md)
- [Сквозной мониторинг транзакций](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Шаги установки

Для включения решения мы будем выполнять следующие действия:
- Развертывание приложения (если еще не развернуто).
- Убедитесь, что приложение является частью сервисной сетки.
- Наблюдайте собранную телеметрию.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Настройте приложение на работу с сеткой службы

Istio поддерживает два способа [инструментирования стручка](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
В большинстве случаев проще всего отметить пространство имен Kubernetes, содержащее ваше приложение, с помощью этикетки *istio-инъекций:*

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Поскольку сервисная сетка снимает данные с провода, мы не можем перехватить зашифрованный трафик. Для трафика, который не покидает кластер, используйте незашифрованный протокол (например, HTTP). Для внешнего трафика, который должен быть зашифрован, рассмотрите [возможность настройки прекращения TLS](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) на контроллере входа.

Приложения, запущенные вне сетки службы, не затрагиваются.

### <a name="deploy-your-application"></a>Развертывание приложения

- Развертывайте приложение в пространстве имен *моего приложения-namespace.* Если приложение уже развернуто, и вы следовали описаню выше автоматическому методу впрыска sidecar, необходимо воссоздать стручки, чтобы гарантировать, что Istio впрыскивает свой колдок; либо инициировать обновление или удалить отдельные стручки и ждать их воссоздании.
- Убедитесь, что ваша заявка соответствует [требованиям Istio.](https://istio.io/docs/setup/kubernetes/prepare/requirements/)

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Развертывание нулевого мониторинга приложений приборов для размещенных приложений Kubernetes

1. Скачать и извлечь [ *адаптер applicationer applicationer Application.* ](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/)
2. Перейдите на */src/kubernetes/* внутри папки релиза.
3. *Оторитите приложение-инсайты-istio-mixer-adapter-deployment.yaml*
    - отспособите значение переменной *среды ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY,* чтобы содержать ключ приборов ресурса Application Insights на портале Azure для хранения телеметрии.
    - При необходимости отспособите значение переменной *среды ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES,* чтобы содержать разделенный запятой список пространства имен, для которых вы хотели бы включить мониторинг. Оставьте его пустым для мониторинга всех пространствах имен.
4. Применить *каждый* файл YAML найти под *src / kubernetes /* путем запуска следующих (вы все равно должны быть внутри */src/ kubernetes /*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Проверка развертывания

- Убедитесь, что адаптер Application Insights был развернут:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> В некоторых случаях требуется тонкая настройка. Чтобы включить или исключить телеметрию для отдельного стручка из сбора, используйте *appinsights/monitoring.enabled* этикетку на этом стручке. Это будет иметь приоритет над всей конфигурацией, основанной на пространстве имен. Установите *appinsights/monitoring.enabled* к *истинному,* чтобы включить стручок, и *ложно,* чтобы исключить его.

### <a name="view-application-insights-telemetry"></a>Посмотреть телеметрию Application Insights

- Создайте выборочный запрос против вашего приложения, чтобы подтвердить, что мониторинг функционирует должным образом.
- В течение 3-5 минут необходимо начать просмотр телеметрии на портале Azure. Не забудьте ознакомиться с разделом *«Карта приложений»* ресурса «Исследования приложений» на портале.

## <a name="troubleshooting"></a>Устранение неполадок

Ниже приведен поток устранения неполадок, который можно использовать, когда телеметрия не отображается на портале Azure, как ожидалось.

1. Убедитесь, что приложение находится под нагрузкой и отправляет/получает запросы в простом HTTP. Поскольку телеметрия снята с провода, зашифрованный трафик не поддерживается. Если нет входящих или исходящих запросов, телеметрия также не будет.
2. Убедитесь, что правильный ключ приборов предоставляется в *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* переменной среды в *приложении-информации-istio-миксер-адаптер-развертывание.* Ключ приборов находится на вкладке *«Обзор»* ресурса Application Insights на портале Azure.
3. Убедитесь, что правильное пространство имен Kubernetes предоставляется в *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* переменной среды в *приложении-информации-istio-mixer-адаптер-развертывание.* Оставьте его пустым для мониторинга всех пространствах имен.
4. Убедитесь, что стручки вашего приложения были впрыснуты в помощь Istio. Убедитесь, что на каждом капсуле существует загон Istio.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Убедитесь, что на капсуле работает контейнер с именем *istio-прокси.*

5. Просмотр следов адаптера адаптера Application Insights.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Количество полученных элементов телеметрии обновляется один раз в минуту. Если он не растет минуту за минуту - телеметрия не отправляется в адаптер Istio.
   Ищите ошибки в журнале.
6. Если установлено, что *адаптер Application Insight для Kubernetes* не питается телеметрией, проверьте журналы Mixer istio, чтобы выяснить, почему он не отправляет данные в адаптер:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Ищите любые ошибки, особенно касающиеся коммуникаций с адаптером *adapter applicationinsights.*

## <a name="faq"></a>часто задаваемые вопросы

Для получения последней информации о ходе реализации этого проекта посетите [адаптер Application Insights для GitHub проекта Istio Mixer.](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq)

## <a name="uninstall"></a>Удаление

Чтобы удалить продукт, для *каждого* файла YAML, найденного под *src/kubernetes/run:*

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о том, как Azure Monitor и контейнеры работают вместе, посетите [обзор azure Monitor для просмотра контейнеров](../../azure-monitor/insights/container-insights-overview.md)
