---
title: Использование Application Insights для мониторинга службы Azure Kubernetes (AKS) или других Kubernetes размещенных приложений — Azure Monitor | Документация Майкрософт
description: Azure Monitor использует технологии сетки службы, Istio, в кластере Kubernetes для мониторинга приложения для любого приложения, размещенные Kubernetes. Это позволяет собирать данные телеметрии Application Insights, относящиеся к входящих и исходящих запросов и из модулей, запущенном в кластере.
services: application-insights
author: tokaplan
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: alkaplan
ms.openlocfilehash: c94d589875195207ec6f71c35ad077cac281fda5
ms.sourcegitcommit: 9e8dfa1169a55c3c8af93a6c5f4e0dace4de48b2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65555836"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>Мониторинг приложений ноль инструментирования для Kubernetes размещенных приложений

> [!IMPORTANT]
> Эта функция сейчас доступна в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Monitor теперь использует Технический сетки службы в кластере Kubernetes для предоставления из мониторинг приложений поле для любого приложения, размещенные Kubernetes. По умолчанию Application Insight функции, такие как [схема сопоставления приложений](../../azure-monitor/app/app-map.md) для моделирования зависимостей, [Live Stream метрики](../../azure-monitor/app/live-stream.md) для наблюдения в режиме реального времени, эффективными визуализациями с [по умолчанию панель мониторинга](../../azure-monitor/app/overview-dashboard.md), [обозревателе метрик](../../azure-monitor/platform/metrics-getting-started.md), и [книг](../../azure-monitor/app/usage-workbooks.md). Эта функция поможет пользователям выявлять узкие и сбои во всех рабочих нагрузок Kubernetes в выбранном пространстве имен Kubernetes. Используя преимущества многочисленных существующие инвестиции сетки службы с помощью технологии, такие как Istio, Azure Monitor позволяет автоматически инструментированного приложения мониторинга без каких-либо изменений в код приложения.

> [!NOTE]
> Это одна из многих способов выполнять мониторинг приложений в Kubernetes. Также можно инструментировать любого приложения, размещенного в Kubernetes с помощью [пакет SDK Application Insights](../../azure-monitor/azure-monitor-app-hub.md) без использования службами. Для мониторинга Kubernetes без инструментирование приложения с помощью пакета SDK, можно использовать после метода.

## <a name="prerequisites"></a>Технические условия

- Объект [кластера Kubernetes](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Доступ к кластеру, чтобы запустить консоль *kubectl*.
- [Ресурса Application Insight](create-new-resource.md)
- Иметь службами. Если у кластера нет развернутых Istio, вы узнаете как [установить и использовать в службе Azure Kubernetes Istio](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Возможности

С помощью ноль инструментирования приложения для мониторинга для Kubernetes размещенных приложений, можно использовать:

- [Схема сопоставления приложений](../../azure-monitor/app/app-map.md)
- [Live Stream Metrics](../../azure-monitor/app/live-stream.md)
- [Панели мониторинга](../../azure-monitor/app/overview-dashboard.md)
- [Обозреватель метрик](../../azure-monitor/platform/metrics-getting-started.md)
- [Распределенная трассировка](../../azure-monitor/app/distributed-tracing.md)
- [Наблюдение за транзакциями End-to-end](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Шаги установки

Чтобы включить решение, мы будем выполнять следующие действия:
- Развертывание приложения (если они еще не развернуты).
- Убедитесь, что приложение является частью слоя взаимодействия между службами.
- Обратите внимание, собранных данных телеметрии.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Настройка приложения для работы со службами

Istio поддерживает два типа [инструментирование pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
В большинстве случаев проще всего пометить пространств имен Kubernetes, содержащий приложение с *istio внедрения* метки:

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Так как данные повышает сетки службы отключено сети, мы не может перехватывать зашифрованный трафик. Для трафика, не покидают кластер используйте незашифрованный протокол (например, HTTP). Для внешнего трафика, который подлежит шифрованию, рассмотрите возможность [настройка моста SSL](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) по контроллеру входящего трафика.

Не влияет на приложения, работающего вне среды слоя взаимодействия между службами.

### <a name="deploy-your-application"></a>Развертывание приложения

- Развернуть приложение *my namespace приложения* пространства имен. Если приложение уже развернуто, и вы выполнили описанным выше методом внедрения автоматическое расширение, необходимо повторно создать модулей и убедитесь, что Istio внедряет его расширения; инициировать последовательное обновление или удаление отдельных модулей и ожидать их создать заново.
- Убедитесь, приложение соответствует [Istio требования](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Развертывание приложения ноль инструментирования, мониторинга для Kubernetes размещенных приложений

1. Загрузите и извлеките [ *адаптера Application Insights* выпуска](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Перейдите к */src/kubernetes/* внутри папки выпуска.
3. Изменить *application-insights-istio-mixer-adapter-deployment.yaml*
    - изменить значение *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* переменную среды, чтобы содержать ключ инструментирования ресурса Application Insights на портале Azure для хранения данных телеметрии.
    - При необходимости измените значение *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* переменную среды, чтобы содержать разделенный запятыми список пространств имен, для которого вы хотите включить мониторинг. Оставьте пустым, чтобы отслеживать все пространства имен.
4. Применить *каждые* yaml-файл, найти в разделе *src/kubernetes/* , выполнив следующую (необходимо быть по-прежнему внутри */src/kubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Проверить развертывание

- Убедитесь, что адаптер Application Insights будет развернута.

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> В некоторых случаях точной настройки помощник по настройке не требуется. Чтобы включить или исключить из собираемых данных телеметрии для отдельных pod, используйте *appinsights/monitoring.enabled* метки на этой pod. Это имеют приоритет над все конфигурации на основе пространства имен. Задайте *appinsights/monitoring.enabled* для *true* для включения pod, а в *false* чтобы исключить его.

### <a name="view-application-insights-telemetry"></a>Просмотр данных телеметрии Application Insights

- Создайте образец запроса для вашего приложения, чтобы убедиться, что мониторинг работает правильно.
- В течение 3 – 5 минут должны начать появляться данные телеметрии, которые отображаются на портале Azure. Обязательно ознакомьтесь с *схема сопоставления приложений* разделе ресурса Application Insights на портале.

## <a name="troubleshooting"></a>Устранение неполадок

Ниже процесс устранения неполадок для использования при телеметрии не отображается на портале Azure, как предполагается.

1. Убедитесь, приложение под нагрузкой и отправка или получение запросов в простой HTTP. Так как данные телеметрии удален из сети, зашифрованный трафик не поддерживается. Если нет входящих или исходящих запросов, будет существовать отсутствие телеметрии либо.
2. Обеспечить правильный ключ инструментирования предоставляется в *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* переменной среды в *application-insights-istio-mixer-adapter-deployment.yaml*. Найден ключ инструментирования на *Обзор* вкладке ресурса Application Insights на портале Azure.
3. Обеспечить правильное пространство имен Kubernetes предоставляется в *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* переменной среды в *application-insights-istio-mixer-adapter-deployment.yaml*. Оставьте пустым, чтобы отслеживать все пространства имен.
4. Убедитесь, что модули приложения были расширения введенному Istio. Убедитесь, что в Istio расширения существует на каждым pod.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Убедитесь, что контейнер с именем *istio прокси* на модуль.

5. Просмотр трассировок в Application Insights адаптера.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Число элементов телеметрии, полученных обновляется раз в минуту. Если он не увеличение минуту за минуту - отсутствие телеметрии, отправляемых адаптер, Istio.
   Найдите все ошибки в журнале.
6. Если установлено, *Application Insight для Kubernetes* адаптера не вводятся данные телеметрии, проверьте журналы Mixer Istio чтобы выяснить, почему он не отправляет данные в адаптер:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Наличие ошибок, особенно относящихся к связи с *applicationinsightsadapter* адаптера.

## <a name="faq"></a>Часто задаваемые вопросы

Самую актуальную информацию для отслеживания хода выполнения над этим проектом, см. в статье [адаптер Application Insights для проекта Mixer Istio GitHub](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Удаление

Удаление продукта, для *каждые* yaml-файл, найти в разделе *src/kubernetes/* запуска:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об использовании Azure Monitor и контейнеры друг с другом посещение [Azure Monitor для Общие сведения о контейнерах](../../azure-monitor/insights/container-insights-overview.md)