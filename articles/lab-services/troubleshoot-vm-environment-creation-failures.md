---
title: Устранение неполадок при сбоях создания виртуальных Машин и сред Azure DevTest Labs | Документация Майкрософт
description: Узнайте, как для устранения неполадок виртуальной машины (VM) и сбои при создании среды в Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.openlocfilehash: 7baa5e4c113e6c21c6123ac7c8399533a7dfb358
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65410302"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Устранение неполадок виртуальной машины (VM) и сбои при создании среды в Azure DevTest Labs
DevTest Labs выдают предупреждения, если имя компьютера является недопустимым, или если вы собираетесь нарушает политику лаборатории. В некоторых случаях вы увидите красный `X` рядом с лабораторию состояние виртуальной Машины или среды, информирующее о том, что что-то пошло не так.  Эта статья содержит ряд приемов, которые можно использовать, чтобы найти основную причину проблемы и, надеюсь, избежать возникновения проблемы в будущем.

## <a name="portal-notifications"></a>Уведомления портала
Если вы используете портал Azure, является первым делом следует рассмотреть **панель уведомлений**.  Уведомления панели, доступные на панели основных команд, нажав кнопку **значок колокольчика**, сообщает ли создании виртуальной Машины или среды лаборатории успешно или нет.  Если произошел сбой, вы увидите сообщение об ошибке, связанное с ошибкой создания. Также часто приводятся подробные Дополнительные сведения, помогающие устранить проблему. В следующем примере создания виртуальной машины не удалось из-за дефицита ядер. Подробное сообщение указывает, как устранить проблему и запросить увеличение квоты core.

![Уведомления с портала Azure](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)


## <a name="activity-logs"></a>Журналы действий
Просмотрите журналы действий, если вы изучаете сбоя через некоторое время после попытки создания виртуальной Машины или среды. В этом разделе показано, как найти журналы для виртуальных машин и сред.

## <a name="activity-logs-for-virtual-machines"></a>Журналы действий для виртуальных машин

1. На домашней странице для лаборатории, выберите виртуальную Машину, чтобы запустить **виртуальной машины** страницы.
2. На **виртуальной машины** странице **МОНИТОРИНГ** левого меню, выберите **журнал действий** для просмотра всех журналов, связанных с виртуальной Машиной.
3. В элементы журнала действий выберите операцию, не удалось. Как правило, вызывается неудачно завершившейся операцией `Write Virtualmachines`.
4. В области справа перейдите на вкладку JSON. Отобразятся сведения в представлении JSON журнала.

    ![Журнал действий для виртуальной Машины](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Просмотрите в журнале JSON, пока не найдете `statusMessage` свойство. Она предоставляет сообщение об ошибке основные и дополнительные подробные сведения, если это применимо. Приведенный ниже код JSON является ошибкой пример для ядро в кавычках превышено видели ранее в этой статье.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Журнал действий для среды

Чтобы просмотреть журнал действий для создания среды, выполните следующие действия.

1. На домашней странице для лаборатории, выберите **конфигурация и политики** в меню слева.
2. на **конфигурация и политики** выберите **журналы действий** меню.
3. Поиск сбоев в списке действий в журнале и выберите его.
4. В области справа перейдите на вкладку JSON и найдите **statusMessage**.

    ![Журнал действий среды](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Журналы развертывания шаблона Resource Manager
Если ваш среды или виртуальная машина была создана с помощью службы автоматизации, есть одно последнее место для поиска сведений об ошибке. Это журнал развертывания шаблона Azure Resource Manager. При создании ресурсов лаборатории за счет автоматизации часто осуществляется посредством развертывания шаблона Azure Resource Manager. См. в разделе[ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) примеры шаблонов Azure Resource Manager, которые создают ресурсы DevTest Labs.

Чтобы просмотреть журналы развертывания шаблона лаборатории, выполните следующие действия.

1. Запустите страницу для группы ресурсов, в которой существует лаборатории.
2. Выберите **развертываний** в меню слева в разделе **параметры**.
3. Поиск развертываний в состоянии сбоя и выберите его.
4. На **развертывания** выберите **сведения об операции** ссылку для операции со сбоем.
5. Отобразятся сведения об операции со сбоем в **сведения об операции** окна.

## <a name="next-steps"></a>Дальнейшие действия
См. в разделе [Устранение сбоев артефактов](devtest-lab-troubleshoot-artifact-failure.md)