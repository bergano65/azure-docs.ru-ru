---
title: Развертывание интеграции в области работоспособности - Менеджер развертывания Azure
description: В этой статье описывается, как развернуть службу в нескольких регионах с помощью диспетчера развертывания Azure. Кроме того, здесь приведены методики безопасного развертывания, которые позволяют проверить стабильность развертывания во все регионы, прежде чем выполнять этот процесс.
author: mumian
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: jgao
ms.openlocfilehash: aa99bdfcbc2f42ae81bdd55c266bcd7d87808031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273804"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Ввести развертывание интеграции работоспособности в менеджер еразвертывания Azure (публичный предварительный просмотр)

[Менеджер развертывания Azure](./deployment-manager-overview.md) позволяет выполнять постановочные развертывания ресурсов управления ресурсами Azure. Ресурсы распределяются по регионам по регионам в упорядоченном порядке. Интегрированная проверка работоспособности диспетчера развертывания Azure позволяет отслеживать развертывания и автоматически останавливать проблемные развертывания, что позволяет устранить неполадки и уменьшить масштаб воздействия. Эта функция может уменьшить недоступность службы, вызванную регрессиями в обновлениях.

## <a name="health-monitoring-providers"></a>Поставщики медицинских мониторинга

Чтобы максимально упростить интеграцию функций работоспособности, корпорация Майкрософт сотрудничает с некоторыми ведущими компаниями по мониторингу работоспособности служб, чтобы предоставить вам простое решение для копирования и вставки, позволяющее интегрировать проверки работоспособности с вашими развертываниями. Если вы еще не используете монитор здоровья, это отличные решения для начала:

| ![менеджер по развертыванию лазурного развертывания datadog поставщика данных](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![лазурный менеджер развертывания менеджер поставщика мониторинга здоровья сайт24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![менеджер по развертыванию лазурного развертывания поставщика wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog, ведущая платформа мониторинга и аналитики для современных облачных сред. [Узнайте, как Datadog интегрируется с менеджером развертывания Azure.](https://www.datadoghq.com/azure-deployment-manager/)|Site24x7 — решение для мониторинга частных и общедоступных облачных сервисов.: [Узнайте, как Site24x7 интегрируется с менеджером развертывания Azure.](https://www.site24x7.com/azure/adm.html)| Wavefront, платформа мониторинга и аналитики для многооблачных прикладных сред. [Узнайте, как Wavefront интегрируется с менеджером развертывания Azure.](https://go.wavefront.com/wavefront-adm/)|

## <a name="how-service-health-is-determined"></a>Как определяется работоспособность службы

[Поставщики медицинских мониторинга](#health-monitoring-providers) предлагают несколько механизмов мониторинга услуг и оповещения о любых проблемах со здоровьем. Примером одного из таких предложений является [Azure Monitor.](../../azure-monitor/overview.md) Azure Monitor может использоваться для создания оповещений при превышении определенных пороговых значений. Например, при развертывании нового обновления в службе пик использования памяти и процессора превышает ожидаемые уровни. После уведомления вы можете принять меры по исправлению положения.

Эти поставщики медицинских услуг обычно предлагают REST AIS, чтобы статус мониторов вашей службы мог быть изучен программно. AIS REST может либо вернуться с простым здоровым/нездоровым сигналом (определенным кодом ответа HTTP) и/или с подробной информацией о получаемых сигналах.

Новый шаг *healthCheck* в Azure Deployment Manager позволяет декларировать коды HTTP, указывающие на здоровую службу, или, для более сложных результатов REST, можно даже указать регулярные выражения, которые, если они совпадают, указывают на здоровую реакцию.

Поток к настройке с проверкой работоспособности менеджера развертывания Azure:

1. Создайте свои мониторы здоровья через поставщика медицинских услуг по вашему выбору.
1. Создайте один или несколько шагов healthCheck в рамках развертывания диспетчера развертывания Azure. Заполните шаги healthCheck со следующей информацией:

    1. URI для REST API для ваших мониторов здоровья (как определено вашим поставщиком медицинских услуг).
    1. Сведения о проверке подлинности. В настоящее время поддерживается только аутентификация стиля API-key.
    1. [Коды статуса HTTP](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) или регулярные выражения, определяющие здоровый ответ. Обратите внимание, что вы можете предоставить регулярные выражения, которые ВСЕ должны соответствовать ответу, чтобы считаться здоровым, или вы можете предоставить выражения, которые любой должен соответствовать ответ учесть здоровым. Оба метода поддерживаются.

    Следующий Json является примером:

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

1. Вывежьте шаги healthCheck в соответствующее время в развертывании диспетчера развертывания Azure. В следующем примере на этап проверки работоспособности вызывается **постразвертываниеШагов** **stepGroup2.**

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

Чтобы пройти пример, [см.](./deployment-manager-health-check.md)

## <a name="phases-of-a-health-check"></a>Фазы проверки здоровья

На этом этапе менеджер развертывания Azure знает, как запросить работоспособность вашей службы и на каких этапах развертывания это сделать. Однако менеджер развертывания Azure также позволяет провести глубокую конфигурацию времени этих проверок. Шаг healthCheck выполняется в 3 последовательных фазах, все из которых имеют настраиваемую продолжительность: 

1. Ожидание

    1. После завершения операции развертывания всасывание, перенастройка на основе новых данных или даже впервые запущена. Кроме того, службам требуется время, чтобы начать излучать сигналы здоровья, которые должны быть агрегированы поставщиком мониторинга здоровья во что-то полезное. Во время этого бурного процесса, возможно, нет смысла проверять работоспособность службы, так как обновление еще не достигло стабильного состояния. Действительно, служба может колебаться между здоровыми и нездоровыми состояниями по мере того, как ресурсы оседают. 
    1. На этапе ожидания работоспособность службы не контролируется. Это используется для того, чтобы развернуть ресурсы время, чтобы испечь до начала процесса проверки работоспособности. 
1. Elastic

    1. Поскольку невозможно знать во всех случаях, сколько времени потребуется ресурсов, чтобы испечь, прежде чем они станут стабильными, упругий этап позволяет для гибкого периода времени между тем, когда ресурсы потенциально нестабильны и когда они необходимы для поддержания здорового устойчивого Государства.
    1. Когда начинается фаза Elastic, менеджер развертывания Azure начинает периодически опрашивать предоставленную endpoint REST для работы службы. Интервал голосования настраивается. 
    1. Если монитор работоспособности возвращается с сигналами, указывающими на неработоспособность службы, эти сигналы игнорируются, фаза Elastic продолжается, и опрос продолжается. 
    1. Как только монитор работоспособности возвращается с сигналами, указывающими на то, что служба здорова, фаза Elastic заканчивается и начинается фаза HealthyState. 
    1. Таким образом, продолжительность, указанная для фазы Elastic, является максимальным количеством времени, которое может быть потрачено на опрос для работоспособности службы, прежде чем здоровый ответ считается обязательным. 
1. HealthyState

    1. На этапе HealthyState здоровье службы постоянно опрашивается с тем же интервалом, что и фаза Elastic. 
    1. Ожидается, что служба будет поддерживать здоровые сигналы от поставщика мониторинга здоровья в течение всего указанного срока. 
    1. Если в какой-либо момент обнаруживается неработоспособный ответ, диспетчер развертывания Azure остановит весь развертывание и вернет ответ REST, несущий неработоспособные сигналы службы.
    1. После окончания длительности работы HealthState проверка работы завершена, и развертывание продолжается до следующего шага.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали о том, как интегрировать мониторинг работоспособности в диспетчера развертывания Azure. Перейдите к следующей статье, чтобы узнать, как выполнить развертывание с помощью диспетчера развертывания.

> [!div class="nextstepaction"]
> [Учебник: интеграция проверки работоспособности в менеджере развертывания Azure](./deployment-manager-tutorial-health-check.md)