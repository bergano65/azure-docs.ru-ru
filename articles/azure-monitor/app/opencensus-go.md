---
title: Трассировка OpenCensus для Go с помощью Azure Application Insights | Документация Майкрософт
description: Инструкции по интеграции средства трассировки OpenCensus для Go с локальным сервером пересылки и Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/15/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cdf01fbbcc8ef1f90b2e0f8973f59c46c5bf70f8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60577911"
---
# <a name="collect-distributed-traces-from-go-preview"></a>Сбор данных о распределенных трассировках из Go (предварительная версия)

Application Insights теперь поддерживает распределенную трассировку приложений Go за счет интеграции с [OpenCensus](https://opencensus.io) и нашим новым [локальным сервером пересылки](./opencensus-local-forwarder.md). В этой статье описано, как установить OpenCensus для Go и получить данные трассировки в Application Insights.

## <a name="prerequisites"></a>Технические условия

- Вам понадобится подписка Azure.
- Установите Go. В рамках этой статьи используется [Go версии 1.11](https://golang.org/dl/).
- Следуйте инструкциям по установке [локального сервера пересылки в качестве службы Windows](./opencensus-local-forwarder.md).

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портале Azure](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Создание ресурса Application Insights

Сначала необходимо создать ресурс Application Insights, который создаст ключ инструментирования (ikey). Затем с помощью этого ключа ikey локальный сервер настраивается для отправки распределенных трассировок из инструментированного приложения OpenCensus в Application Insights.   

1. Выберите **Создать ресурс** > **Средства разработчика** > **Application Insights**.

   ![Добавления ресурса Application Insights](./media/opencensus-Go/0001-create-resource.png)

   Откроется окно настроек, в котором нужно заполнить все поля в соответствии с приведенной ниже таблицей.

    | Параметры        | Значение           | Описание  |
   | ------------- |:-------------|:-----|
   | **Имя**      | Глобально уникальное значение | Имя, идентифицирующее отслеживаемое приложение |
   | **Тип приложения** | Общие сведения | Тип отслеживаемого приложения |
   | **Группа ресурсов**     | myResourceGroup      | Имя новой группы ресурсов для размещения данных App Insights |
   | **Местоположение.** | Восточная часть США | Выберите ближайшее расположение или расположение вблизи места размещения приложения |

2. Нажмите кнопку **Создать**.

## <a name="configure-local-forwarder"></a>Настройка локального сервера пересылки

1. Выберите **Обзор** > **Основные компоненты** и скопируйте **ключ инструментирования** приложения.

   ![Снимок экрана с ключом инструментирования](./media/opencensus-Go/0003-instrumentation-key.png)

2. Измените файл `LocalForwarder.config` и добавьте ключ инструментирования. Если вы следовали инструкциям из [предварительных требований](./opencensus-local-forwarder.md), вы найдете этот файл по адресу `C:\LF-WindowsServiceHost`.

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. Перезапустите службу **локального сервера пересылки** приложения.

## <a name="opencensus-go-packages"></a>Пакеты OpenCensus для Go

1. Установите пакеты OpenCensus для Go из командной строки:

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. Добавьте приведенный ниже код в файл с расширением .go, а затем выполните компиляцию и запуск. (Этот пример взят из официальных рекомендаций OpenCensus. В него добавлен код, который упрощает интеграцию с локальным сервером пересылки.)

     ```go
        // Copyright 2018, OpenCensus Authors
        //
        // Licensed under the Apache License, Version 2.0 (the "License");
        // you may not use this file except in compliance with the License.
        // You may obtain a copy of the License at
        //
        //     https://www.apache.org/licenses/LICENSE-2.0
        //
        // Unless required by applicable law or agreed to in writing, software
        // distributed under the License is distributed on an "AS IS" BASIS,
        // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
        // See the License for the specific language governing permissions and
        // limitations under the License.
        package main
        
        import (
        
            "bytes"
            "fmt"
            "log"
            "net/http"
            os "os"
            
            ocagent "contrib.go.opencensus.io/exporter/ocagent"
            "go.opencensus.io/plugin/ochttp"
            "go.opencensus.io/plugin/ochttp/propagation/tracecontext"
            "go.opencensus.io/trace"
        
        )
        
        func main() {
            // Register stats and trace exporters to export the collected data.
            serviceName := os.Getenv("SERVICE_NAME")
            if len(serviceName) == 0 {
                serviceName = "go-app"
            }
            fmt.Printf(serviceName)
            agentEndpoint := os.Getenv("OCAGENT_TRACE_EXPORTER_ENDPOINT")

            if len(agentEndpoint) == 0 {
                agentEndpoint = fmt.Sprintf("%s:%d", ocagent.DefaultAgentHost, ocagent.DefaultAgentPort)
            }
        
            fmt.Printf(agentEndpoint)
            exporter, err := ocagent.NewExporter(ocagent.WithInsecure(), ocagent.WithServiceName(serviceName), ocagent.WithAddress(agentEndpoint))
        
            if err != nil {
                log.Printf("Failed to create the agent exporter: %v", err)
            }
        
            trace.RegisterExporter(exporter)
        
            trace.ApplyConfig(trace.Config{DefaultSampler: trace.AlwaysSample()})
        
            client := &http.Client{Transport: &ochttp.Transport{Propagation: &tracecontext.HTTPFormat{}}}
        
            http.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                var jsonStr = []byte(`[ { "url": "http://blank.org", "arguments": [] } ]`)
                r, _ := http.NewRequest("POST", "http://blank.org", bytes.NewBuffer(jsonStr))
                r.Header.Set("Content-Type", "application/json")
        
                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }
            })
        
            http.HandleFunc("/call_blank", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                r, _ := http.NewRequest("GET", "http://blank.org", nil)

                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
        
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }        
            })
        
            log.Fatal(http.ListenAndServe(":50030", &ochttp.Handler{Propagation: &tracecontext.HTTPFormat{}}))
        
        }
     ```

3. После запуска простого приложения на Go перейдите к `http://localhost:50030`. Каждое обновление браузера будет создавать текст Hello World, а также соответствующие данные span, которые получает локальный сервер пересылки.

4. Чтобы подтвердить, что **локальный сервер пересылки** собирает трассировки, проверьте файл `LocalForwarder.config`. Если вы выполнили шаги из [предварительных требований](https://docs.microsoft.com/azure/application-insights/local-forwarder), он будет находиться в каталоге `C:\LF-WindowsServiceHost`.

    На приведенном ниже изображении файла журнала можно увидеть, что перед запуском второго скрипта, где был добавлен экспортер, `OpenCensus input BatchesReceived` было равно 0. Когда мы начали выполнять обновленный скрипт, `BatchesReceived` увеличилось в соответствии с введенным числом значений:
    
    ![Форма создания ресурса App Insights](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Запуск мониторинга на портале Azure

1. Теперь можно повторно открыть страницу **Обзор** Application Insights на портале Azure для просмотра сведений о выполняющемся в данный момент приложении. Выберите **Live Metrics Stream**.

   ![Снимок экрана области "Обзор" с пунктом Live Metrics Stream в красном прямоугольнике](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. Если выполнить второе приложение Go снова и начать обновлять браузер для `http://localhost:50030`, отобразятся динамические данные трассировки, так как они поступают в Application Insights из службы локального сервера пересылки.

   ![Снимок экрана с разделом Live Metrics Stream и данными производительности](./media/opencensus-go/0006-stream.png)

3. Вернитесь на страницу **Обзор** и выберите **Схема приложений**, чтобы получить визуальный макет отношений зависимости и длительность вызовов между компонентами приложения.

    ![Снимок экрана с основной схемой приложений](./media/opencensus-go/0007-application-map.png)

    Так как мы выполняли трассировку только одного вызова метода, наша схема приложений не так интересна. Но схему приложений можно масштабировать для визуализации гораздо большего количества распределенных приложений:

   ![Схема приложений](media/opencensus-go/application-map.png)

4. Выберите **Анализ работы**, чтобы выполнить подробный анализ работы и определить первопричину низкой производительности.

    ![Снимок экрана с панелью производительности](./media/opencensus-go/0008-performance.png)

5. Если выбрать **Примеры**, а затем щелкнуть один из примеров, которые отображаются на панели справа, запустится интерфейс сведений о сквозной транзакции. В то время как в нашем примере приложения показано одно событие, в более сложном приложении можно изучить сквозную транзакцию до уровня стека вызовов отдельного события.

     ![Снимок экрана с интерфейсом сквозных транзакций](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>Трассировка OpenCensus для Go

Мы рассмотрели только основы интеграции OpenCensus для Go с помощью локального сервера пересылки и Application Insights. В [официальном руководстве по использованию OpenCensus Go](https://godoc.org/go.opencensus.io) рассматриваются более сложные темы.

## <a name="next-steps"></a>Дальнейшие действия

* [Сопоставление приложений](./../../azure-monitor/app/app-map.md)
* [Поиск и диагностика проблем производительности с помощью Azure Application Insights](./../../azure-monitor/learn/tutorial-performance.md)
