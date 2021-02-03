---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 8f3031669723cd61715c12a42f99869ac0eaf3bc
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99500434"
---
Перед развертыванием виртуальных машин на Azure Stack пограничном устройстве необходимо настроить клиент для подключения к устройству через Azure Resource Manager Azure PowerShell. Чтобы получить подробные инструкции, перейдите к разделу [Подключение к Azure Resource Manager на устройстве Azure Stack](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).

Для доступа к устройству из клиента необходимо выполнить следующие действия. (Вы выполнили эту настройку при подключении к Azure Resource Manager. Теперь вы просто проверяете успешность настройки.) 

1. Убедитесь, что Azure Resource Manager связь работает. Введите:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Вызов интерфейсов API локальных устройств для проверки подлинности. Введите: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Укажите имя пользователя- *еджеармусер* и пароль для подключения с помощью Azure Resource Manager.

1. Если вы настроили **Вычисление** для Kubernetes, этот шаг можно пропустить. Продолжайте работу, чтобы убедиться, что вы включили сетевой интерфейс для вычислений. В локальном пользовательском интерфейсе выберите параметры **вычислений** . Выберите сетевой интерфейс, который вы хотите использовать для создания виртуального коммутатора. Создаваемые виртуальные машины будут подключены к виртуальному коммутатору, подключенному к этому порту и связанной сети. Не забудьте выбрать сеть, соответствующую IP-адресу, который будет использоваться для виртуальной машины.  

    ![Снимок экрана, показывающий, как включить параметры вычислений.](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Включите службы вычислений на сетевом интерфейсе. Azure Stack ребро создаст виртуальный коммутатор, соответствующий этому сетевому интерфейсу, и будет управлять им. Не вводите в настоящее время определенные IP-адреса для Kubernetes. Включение вычислений может занять несколько минут.

    > [!NOTE]
    > При создании виртуальных машин GPU выберите сетевой интерфейс, подключенный к Интернету. Это позволяет установить расширение GPU на устройстве.


