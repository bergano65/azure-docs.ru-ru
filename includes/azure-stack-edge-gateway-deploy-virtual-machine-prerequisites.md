---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 56fc24966fa60c3a5e91f92b57332ae2f6a525ff
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256556"
---
Перед развертыванием виртуальных машин на Azure Stack пограничном устройстве необходимо настроить клиент для подключения к устройству через Azure Resource Manager Azure PowerShell. Чтобы получить подробные инструкции, перейдите к разделу [Подключение к Azure Resource Manager на устройстве Azure Stack](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).


Чтобы получить доступ к устройству из клиента, необходимо выполнить следующие действия (Эта конфигурация была выполнена при подключении к Azure Resource Manager. Вы просто проверяете успешность настройки.): 

1. Убедитесь, что Azure Resource Manager связь работает. Тип:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Вызов интерфейсов API локальных устройств для проверки подлинности. Тип: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Укажите имя пользователя- *еджеармусер* и пароль для подключения с помощью Azure Resource Manager.

1. Если вы настроили **Вычисление** для Kubernetes, этот шаг можно пропустить. Продолжайте работу, чтобы убедиться, что вы включили сетевой интерфейс для вычислений. В окне локальный пользовательский интерфейс перейдите к разделу Параметры **вычислений** . Выберите сетевой интерфейс, который будет использоваться для создания виртуального коммутатора. Создаваемые виртуальные машины будут подключены к виртуальному коммутатору, подключенному к этому порту и связанной сети. Не забудьте выбрать сеть, соответствующую IP-адресу, который будет использоваться для виртуальной машины.  

    ![Включить параметры вычислений 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Включите службы вычислений на сетевом интерфейсе. Azure Stack ребро создаст виртуальный коммутатор, соответствующий этому сетевому интерфейсу, и будет управлять им. Не вводите в настоящее время определенные IP-адреса для Kubernetes. Включение вычислений может занять несколько минут.

    > [!NOTE]
    > При создании виртуальных машин GPU выберите сетевой интерфейс, подключенный к Интернету. Это позволяет установить расширение GPU на устройстве.


