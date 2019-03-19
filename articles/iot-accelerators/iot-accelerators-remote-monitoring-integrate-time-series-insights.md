---
title: Интеграция службы "Аналитика временных рядов" с решением удаленного мониторинга Azure| Документация Майкрософт
description: В этом практическом руководстве вы узнаете, как настроить Аналитику временных рядов для существующего решения удаленного мониторинга, в котором еще нет этой службы.
author: aditidugar
manager: timlt
ms.author: adugar
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 850d8bbb525763e0e7d0c0441173180b7c469dd8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085156"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Интеграция службы "Аналитика временных рядов Azure" с решением удаленного мониторинга

Служба "Аналитика временных рядов Azure" — это полностью управляемая служба аналитики, хранения и визуализации для управления данными временных рядов Интернета вещей в облаке. С помощью Аналитики временных рядов можно хранить данные временных рядов и управлять ими, анализировать и визуализировать события одновременно, обнаруживать первопричину, а также сравнивать несколько сайтов и ресурсов.

Акселератор решений для удаленного мониторинга теперь обеспечивает автоматическое развертывание и интеграцию с Аналитикой временных рядов. В этом практическом руководстве вы узнаете, как настроить Аналитику временных рядов для существующего решения удаленного мониторинга, в котором еще нет этой службы.

> [!NOTE]
> Аналитика временных рядов в настоящее время недоступна в облаке Azure для Китая. Развертывания акселератора решений для удаленного мониторинга в облаке Azure для Китая используют Cosmos DB для хранения всех данных.

## <a name="prerequisites"></a>Технические условия

Для работы с этим руководством необходимо развернутое решение удаленного мониторинга:

* [Развертывание акселератора решений для удаленного мониторинга](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Создание группы потребителей

Создайте выделенную группу потребителей в центре Интернета вещей, которая будет использоваться для потоковой передачи данных в службу "Аналитика временных рядов".

> [!NOTE]
> Группы потребителей используются приложениями для извлечения данных из Центра Интернета вещей Azure. В каждой группе потребителей может быть до пяти потребителей выходных данных. Для каждых пяти приемников требуется новая группа потребителей. Можно создать до 32 таких групп.

1. На портале Azure нажмите кнопку Cloud Shell.

1. Выполните приведенную ниже команду, чтобы создать группу потребителей. Используйте имя центра Интернета вещей в вашем развертывании удаленного мониторинга и имя развертывания удаленного мониторинга в качестве имени группы ресурсов:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Развертывание службы "Аналитика временных рядов"

Далее разверните Аналитику временных рядов в качестве дополнительного ресурса в решении удаленного мониторинга и подключите ее к центру Интернета вещей.

1. Войдите на [портале Azure](https://portal.azure.com/).

1. Выберите **Создать ресурс** > **Интернет вещей** > **Аналитика временных рядов**.

    ![Новая среда "Аналитика временных рядов Azure"](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Чтобы создать среду аналитики временных рядов, используйте значения в следующей таблице:

    | Параметр | Значение |
    | ------- | ----- |
    | Имя среды | На следующем снимке экрана используется имя **contorosrmtsi**. Выберите собственное уникальное имя после завершения этого шага. |
    | Подписка | Выберите подписку Azure в раскрывающемся списке. |
    | Группа ресурсов | **Использовать имеющуюся**. Выберите имя существующей группы ресурсов удаленного мониторинга. |
    | Расположение | Мы используем регион **Восточная часть США**. Создайте среду в том же регионе, что и решение удаленного мониторинга, если это возможно. |
    | Sku |**S1** |
    | Capacity | **1** |

    ![Создание службы "Аналитика временных рядов"](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Нажмите кнопку **Создать**. Создание среды может занять некоторое время.

## <a name="create-event-source"></a>Создание источника событий

Создайте источник событий для подключения к Центру Интернета вещей. Используйте группу потребителей, созданную на предыдущих шагах. Для службы "Аналитика временных рядов" требуется, чтобы в каждой службе была выделенная группа потребителей, не используемая другой службой.

1. Перейдите к новой среде Аналитики временных рядов.

1. В левой части экрана выберите **Источники событий**.

    ![Просмотр источников событий](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Щелкните **Добавить**.

    ![Добавление источника событий](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Чтобы настроить Центр Интернета вещей в качестве нового источника событий, используйте значения в следующей таблице:

    | Параметр | Значение |
    | ------- | ----- |
    | Имя источника события | На следующем снимке экрана используется имя **contosorm-iot-hub**. Используйте собственное уникальное имя после завершения этого шага. |
    | Источник | **Центр Интернета вещей** |
    | Вариант импорта | **Использовать Центр Интернета вещей в доступных подписках** |
    | Идентификатор подписки | Выберите подписку Azure в раскрывающемся списке. |
    | Имя Центра Интернета вещей | **contosorma57a6**. Используйте имя Центра Интернета вещей из решения удаленного мониторинга. |
    | Имя политики Центра Интернета вещей | **iothubowner** гарантирует, что используется политика владельца. |
    | Ключ политики Центра Интернета вещей | Это поле заполняется автоматически. |
    | Группа потребителей Центра Интернета вещей | **timeseriesinsights** |
    | Формат сериализации событий | **JSON**     | 
    | Имя свойства для метки времени | Не указывайте |

    ![Создание источника событий](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Нажмите кнопку **Создать**.

## <a name="configure-the-data-access-policy"></a>Настройка политики доступа к данным

Чтобы все пользователи, имеющие доступ к решению удаленного мониторинга, могли просматривать данные в обозревателе Аналитики временных рядов, добавьте приложение и пользователей в политики доступа к данным на портале Azure. 

1. В списке переходов выберите **Группы ресурсов**.

1. Выберите группу ресурсов **ContosoRM**.

1. В списке ресурсов Azure выберите **contosormtsi**.

1. Выберите **Политики доступа к данным**, чтобы просмотреть текущий список назначения ролей.

1. Выберите **Добавить**, чтобы открыть область **Выбор роли пользователя**.

   Если у вас нет разрешений на назначение ролей, параметр **Добавить** не будет отображаться.

1. В раскрывающемся списке **Роль** выберите роль, например **Читатель** или **Участник**.

1. В списке **Выбор** выберите пользователя, группу или приложение. Если субъект безопасности не отображается в списке, введите его имя в поле **Выбор**, чтобы найти в каталоге отображаемые имена, адреса электронной почты и идентификаторы объектов.

1. Нажмите кнопку **Сохранить**, чтобы создать назначение роли. Через несколько секунд субъекту безопасности будет назначена роль в политиках доступа к данным.

> [!NOTE]
> Если требуется предоставить доступ к службе "Аналитика временных рядов" дополнительным пользователям, можно использовать следующие шаги, чтобы [предоставить доступ к данным](../time-series-insights/time-series-insights-data-access.md#grant-data-access).

## <a name="configure-azure-stream-analytics"></a>Настройка Azure Stream Analytics 

На следующем этапе необходимо настроить микрослужбу диспетчера Azure Stream Analytics так, чтобы сообщения больше не отправлялись в Cosmos DB, а сохранялись только в службе "Аналитика временных рядов". Если сообщения должны дублироваться в Cosmos DB, пропустите этот шаг.

1. В списке переходов выберите **Группы ресурсов**.

1. Выберите группу ресурсов **ContosoRM**.

1. В списке ресурсов найдите задание потоковой передачи Azure Stream Analytics (ASA). Имя ресурса начинается со **streamingjobs-**.

1. В верхней части страницы нажмите кнопку, чтобы остановить задания потоковой передачи ASA.

1. Измените запрос ASA, удалив предложения **SELECT**, **INTO** и **FROM**, которые указывают на поток сообщений в Cosmos DB. Эти предложения должны находиться в конце запроса и выглядят примерно так:

    ```sql
    SELECT
            CONCAT(T.IoTHub.ConnectionDeviceId, ';', CAST(DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) AS nvarchar(max))) as id,
            1 as [doc.schemaVersion],
            'd2cmessage' as [doc.schema],
            T.IoTHub.ConnectionDeviceId as [device.id],
            'device-sensors;v1' as [device.msg.schema],
            'StreamingJobs' as [data.schema],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', System.Timestamp) as [device.msg.created],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) as [device.msg.received],
            udf.removeUnusedProperties(T) as Data
    INTO
        Messages
    FROM
        DeviceTelemetry T PARTITION BY PartitionId TIMESTAMP BY T.EventEnqueuedUtcTime
    ```

6. Перезапустите задания потоковой передачи Azure Stream Analytics.

7. Извлеките последние изменения в микрослужбу диспетчера Azure Stream Analytics, введя в командной строке следующую команду:

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>Настройка микрослужбы телеметрии

Извлеките последнюю микрослужбу телеметрии, введя в командной строке следующую команду:

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*(Необязательно)* Связывание пользовательского веб-интерфейса с обозревателем Аналитики временных рядов

Чтобы можно было легко просматривать данные в обозревателе Аналитики временных рядов, мы рекомендуем настроить пользовательский интерфейс, связав его со средой. Для этого извлеките последние изменения в пользовательский веб-интерфейс с помощью следующей команды:

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>Настройка переменных среды

Чтобы завершить интеграцию с Аналитикой временных рядов, необходимо настроить среду развертывания для использования обновленных микрослужб.

### <a name="basic-deployments"></a>Базовые развертывания

Настройте среду развертывания `basic` для использования обновленных микрослужб.

1. На портале Azure на панели слева выберите вкладку **Azure Active Directory**.

1. Щелкните **Регистрация приложений**.

1. Найдите и выберите приложение **ContosoRM**.

1. Перейдите в раздел **Параметры** > **Ключи** и создайте ключ для приложения. Скопируйте значение ключа в безопасное место.

1. Извлеките [последнюю версию YAML-файла Docker Compose](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) из репозитория GitHub с помощью тега latest. 

1. Установите SSH-подключение к виртуальной машине, выполнив инструкции в разделе [Создание и использование ключей SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

1. После подключения введите `cd /app`.

1. Добавьте следующие переменные среды в каждую микрослужбу в YAML-файле Docker Compose и скрипте `env-setup` в виртуальной машине:

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Перейдите к **службе телеметрии** и также измените файл Docker Compose, добавив те же переменные среды.

1. Перейдите к **службе диспетчера ASA** и измените файл Docker Compose, добавив `PCS_TELEMETRY_STORAGE_TYPE`.

1. Перезапустите контейнеры Docker, выполнив команду `sudo ./start.sh` из виртуальной машины.

### <a name="standard-deployments"></a>Стандартные развертывания

Настройте среду развертывания `standard` для использования обновленных микрослужб, указанных выше.

1. В командной строке выполните команду `kubectl proxy`. Дополнительные сведения см. в статье о [доступе к API Kubernetes](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server).

1. Откройте консоль управления Kubernetes.

1. Найдите карту конфигурации и добавьте следующие новые переменные среды для TSI:

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Измените шаблон YAML-файла для группы pod службы телеметрии:

    ```yaml
    - name: PCS_AAD_TENANT
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.tenant
    - name: PCS_AAD_APPID
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.audience
    - name: PCS_AAD_APPSECRET
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.serviceprincipal.secret
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    - name: PCS_TSI_FQDN
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.tsi.fqdn
    ```

5. Измените шаблон YAML-файла для группы pod службы диспетчера ASA:

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>Дальнейшие действия

* Сведения об изучении данных и диагностике оповещений в обозревателе Аналитики временных рядов см. в учебнике по [проведению анализа первопричин](iot-accelerators-remote-monitoring-root-cause-analysis.md).

* Сведения о том, как изучать и запрашивать данные в обозревателе Аналитики временных рядов, см. в документации по [обозревателю Аналитики временных рядов Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
