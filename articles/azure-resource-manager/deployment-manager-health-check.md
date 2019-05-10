---
title: Представить работоспособности интеграции развертывания для диспетчера развертывания Azure
description: В этой статье описывается, как развернуть службу в нескольких регионах с помощью диспетчера развертывания Azure. Кроме того, здесь приведены методики безопасного развертывания, которые позволяют проверить стабильность развертывания во все регионы, прежде чем выполнять этот процесс.
services: azure-resource-manager
documentationcenter: na
author: mumian
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/19
ms.author: jgao
ms.openlocfilehash: 1006cc902cf4b6f763d86165a039a1fec5dc97a1
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467087"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Представить работоспособности интеграции развертывания для диспетчера развертывания Azure (Предварительная версия)

[Azure Deployment Manager](./deployment-manager-overview.md) дает возможность выполнять поэтапном развертывании ресурсов Azure Resource Manager. Ресурсы развертываются регион по областям упорядоченный образом. Проверки работоспособности интеграции диспетчера развертывания Azure можно отслеживать развертывания, а также автоматически проблемных готовите stop, чтобы могли устранения и уменьшения масштаба влияние. Эта функция может уменьшить недоступности службы, из-за регрессии в обновлениях.

## <a name="health-monitoring-providers"></a>Поставщики мониторинга работоспособности

Чтобы сделать максимально упростить интеграцию работоспособности, Microsoft продолжила работу с некоторыми мониторинга компании для предоставления вам с решением простого копирования и вставки для интеграции проверки работоспособности в развертываниях top службы работоспособности. Если вы еще не используете монитор работоспособности, ниже приведены эффективные решения для начала.

| ![Развертывание Azure диспетчер работоспособности монитора поставщика datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![Развертывание Azure диспетчер работоспособности монитора поставщика site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![файл wavefront поставщика монитор работоспособности диспетчера развертывания Azure](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog, ведущим мониторинг и платформы аналитики для современных облачных сред. См. в разделе [интеграции Datadog с помощью диспетчера развертывания Azure](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, все в одном частных и общедоступных облачных служб решение для мониторинга. См. в разделе [как Site24x7 интегрируется с помощью диспетчера развертывания Azure](https://www.site24x7.com/azure/adm.html).| Файл Wavefront, платформа мониторинга и аналитики в среде несколько облачных приложений. См. в разделе [как Wavefront интегрируется с помощью диспетчера развертывания Azure](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Определение работоспособности служб

[Поставщики мониторинга работоспособности](#health-monitoring-providers) предоставляют несколько механизмов для мониторинга служб и оповещать вас о любых неполадках работоспособности службы. [Azure Monitor](/services/monitor/) является примером одного предложения. Azure Monitor можно использовать для создания оповещений при превышении определенных пороговых значений. Например памяти и ЦП пик уровня за пределы ожидаемого уровня при развертывании нового обновления для вашей службы. После получения сообщения, можно предпринять корректирующие действия.

Эти поставщики работоспособности обычно предлагают интерфейсов REST API, таким образом, чтобы программным способом можно проверить состояние службы мониторов. Интерфейсы REST API можно либо вернуться с простой работоспособных и неработоспособных сигнала (определяется по коду ответа HTTP) и/или с подробными сведениями о сигналы, она получает.

Новый *healthCheck* шаг диспетчера развертывания Azure позволяет объявлять коды HTTP, которые указывают на работоспособность службы, или для более сложных результатов REST, можно даже указать регулярные выражения, указывающие, если они совпадают, индикаторы работоспособных устройств ответ.

Поток для получения установки с помощью проверки работоспособности диспетчера развертывания Azure:

1. Создание мониторов работоспособности через поставщика службы работоспособности по своему усмотрению.
1. Создайте один или несколько шагов healthCheck как часть процесса развертывания диспетчера развертывания Azure. Укажите действия healthCheck со следующими сведениями:

    1. URI для REST API для мониторов работоспособности (как определено поставщиком услуг работоспособности).
    1. Сведения о проверке подлинности. В настоящее время поддерживается только проверка подлинности API-ключ стиля.
    1. [Коды состояния HTTP](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) или регулярным выражениям, определяющим работоспособное ответа. Обратите внимание на то, что может предоставить регулярные выражения, которым приходится все совпадения для ответа, чтобы считать работоспособным, или же может предоставлять выражения, в которых любой должны совпадать для ответа считать работоспособным. Поддерживаются оба метода.

    Приведенный ниже код Json является примером:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. Вызов действия healthCheck в соответствующее время процесса развертывания диспетчера развертывания Azure. В следующем примере вызывается на этапе проверки работоспособности в **postDeploymentSteps** из **stepGroup2**.

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

Подробно рассмотрим пример, см. в разделе [руководства: Использовать проверки работоспособности диспетчера развертывания Azure](./deployment-manager-health-check.md).

## <a name="phases-of-a-health-check"></a>Этапы проверки работоспособности

На этом этапе диспетчера развертывания Azure знает, как запросить состояние службы и на какие этапы процесса развертывания для этого. Тем не менее диспетчера развертывания Azure также позволяет выполнять глубокий конфигурацию времени из этих проверок. Шаг healthCheck выполняется в 3 последовательные этапы, каждый из которых имеет настраиваемые длительности: 

1. Ожидание

    1. После завершения операции развертывания виртуальных машин может быть в процессе перезагрузки, повторная настройка на основе новых данных или даже работы в первый раз. Также требуется время для запуска выпуска сигналы работоспособности должны быть объединены в мониторинга поставщика в том, что-нибудь полезное работоспособности служб. В ходе этого процесса Забавная не смысл для проверки для службы работоспособности, так как обновление еще не достиг устойчивого состояния. Действительно службе может колебательное между работоспособные и неработоспособные состояния, как сопоставить ресурсы. 
    1. На этапе ожидания не отслеживается работоспособность службы. Это позволяет разрешить развернутые ресурсы время внедрим перед началом процесса проверки работоспособности. 
1. Эластичная

    1. Поскольку невозможно знать во всех случаях сколько внедрим, прежде чем они станут стабильными, эластичной этап позволяет гибкая периода между Если ресурсы находятся в потенциально нестабильном и по мере необходимости для поддержания работоспособности устойчивого займет ресурсы состояние.
    1. После начала фазы эластичной, менеджер по развертыванию Azure начинает периодически опрашивать указанная конечная точка REST для службы работоспособности. Интервал опроса можно настроить. 
    1. Если монитор работоспособности возвращаются с помощью сигналов, указывающее, что служба находится в неработоспособном состоянии, учитываются эти сигналы, продолжение эластичной этапа и продолжает опроса. 
    1. Как только монитор работоспособности возвращается с помощью сигналов, указывающее на то, что служба работоспособна, эластичной заканчивается и на этапе HealthyState начало этапа. 
    1. Таким образом времени, указанному для эластичных этапа — это максимальное время, которое может быть затрачено опроса для службы работоспособности, прежде чем работоспособное ответ считается обязательным. 
1. HealthyState

    1. На этапе HealthyState работоспособность службы постоянно опрашивается на тот же интервал что стадия эластичной. 
    1. Службы должен поддерживать работоспособное сигналы от поставщика мониторинга работоспособности в течение всего указанного. 
    1. В любой момент в случае обнаружения ответ неработоспособное останавливается всего развертывания и вернуть Ответ REST, несущий сигналы неработоспособную службу диспетчера развертывания Azure.
    1. После окончания длительности HealthyState healthCheck завершена, и развертывание продолжает к следующему шагу.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали о том, как интегрировать диспетчера развертывания Azure, наблюдение за работоспособностью. Перейдите к следующей статье, чтобы узнать, как выполнить развертывание с помощью диспетчера развертывания.

> [!div class="nextstepaction"]
> [Руководство по интеграции проверки работоспособности диспетчера развертывания Azure](./deployment-manager-tutorial-health-check.md)