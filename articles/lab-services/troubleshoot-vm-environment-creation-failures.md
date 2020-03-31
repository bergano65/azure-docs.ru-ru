---
title: Устранение неполадок VM и сбои в работе среды Azure DevTest Labs
description: Узнайте, как устранить проблемы виртуальной машины (VM) и сбои создания среды в Лабораториях Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 155a359608cf6d846578306545f5ce0b4003949c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166349"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Устранение проблем виртуальной машины (VM) и сбои создания среды в Лабораториях Azure DevTest
DevTest Labs дает вам предупреждения, если имя машины является недействительным или если вы собираетесь нарушить политику лаборатории. Иногда рядом `X` с лабораторией VM или статусом среды вы видите красный цвет, который сообщает вам, что что-то пошло не так.  В этой статье приведены несколько трюков, которые можно использовать, чтобы найти основную проблему и, надеюсь, избежать этой проблемы в будущем.

## <a name="portal-notifications"></a>Уведомления портала
Если вы используете портал Azure, первое место, чтобы посмотреть на это **панели уведомлений**.  Панель уведомлений, доступная на главной панели команд, нажав **значок колокола,** расскажет вам, была ли лаборатория VM или создание среды успешным или нет.  Если произошел сбой, вы видите сообщение об ошибке, связанное с сбоем создания. Подробности часто дают дополнительную информацию, чтобы помочь вам решить эту проблему. В следующем примере создание виртуальной машины не удалось из-за иссякания ядер. Подробное сообщение говорит вам, как устранить проблему и запросить увеличение основной квоты.

![Уведомление портала Azure](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>ВМ в состоянии коррупции
Если вы видите статус вашего VM в лаборатории как **поврежденный,** базовый VM может быть удален со страницы **Виртуальной машины,** на которую пользователь может перейти со страницы **Виртуальных Машин** (не со страницы DevTest Labs). Очистите свою лабораторию в DevTest Labs, удаляя VM из лаборатории. Затем воссоздайте свой VM в лаборатории. 

![ВМ в коррумпированном состоянии](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>Журналы действий
Посмотрите журналы активности, если вы расследуете сбой через некоторое время после попытки создания вашего VM или среды. В этом разделе показано, как найти журналы для vMs и сред.

## <a name="activity-logs-for-virtual-machines"></a>Журналы активности для виртуальных машин

1. На главной странице вашей лаборатории выберите VM для запуска страницы **Virtual Machine.**
2. На странице **«Виртуальная машина»** в разделе **MONITORING** левого меню выберите **журнал activity,** чтобы просмотреть все журналы, связанные с VM.
3. В элементах журнала активности выберите сбой операции. Как правило, операция `Write Virtualmachines`называется.
4. В правом стеле переключитесь на вкладку JSON. Вы видите детали в представлении JSON журнала.

    ![Журнал активности для VM](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Просмотрите журнал JSON, пока `statusMessage` не найдете свойство. Это дает вам основное сообщение об ошибке и более подробную информацию, если это применимо. Следующий JSON является примером для ядра цитируется превысил ошибку видели ранее в этой статье.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Журнал активности для среды

Чтобы просмотреть журнал активности для создания среды, выполните следующие действия:

1. На главной странице лаборатории выберите **конфигурацию и политики** в левом меню.
2. на странице **конфигурации и политики** выберите **журналы активности** в меню.
3. Ищите сбой в списке действий в журнале и выберите его.
4. В правом стеле переключитесь на вкладку JSON и ищите **статусMessage.**

    ![Журнал экологической активности](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Журналы развертывания шаблонов управления ресурсами
Если ваша среда или виртуальная машина была создана с помощью автоматизации, есть последнее место, чтобы посмотреть информацию об ошибках. Это журнал развертывания шаблонов шаблона управления ресурсами Azure. Когда лабораторный ресурс создается с помощью автоматизации, это часто делается с помощью развертывания шаблона шаблона Azure Resource Manager. Просмотрите[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) примеры шаблонов Azure Resource Manager, которые создают ресурсы DevTest Labs.

Чтобы просмотреть журналы развертывания шаблонов лаборатории, выполните следующие действия:

1. Запустите страницу для группы ресурсов, в которой существует лаборатория.
2. Выберите **развертывания** в левом меню в **настройках.**
3. Ищите развертывания с невыполненным состоянием и выберите его.
4. На странице **развертывания** выберите ссылку **на детали операции** для операции, которая не удалась.
5. В окне **деталей операции** вы видите подробную информацию об операции.

## <a name="next-steps"></a>Дальнейшие действия
Посмотреть [сбои артефактов устранения неполадок](devtest-lab-troubleshoot-artifact-failure.md)
