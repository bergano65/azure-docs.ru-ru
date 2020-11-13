---
title: Динамическое изменение уровня обслуживания тома для Azure NetApp Files | Документация Майкрософт
description: Описывает, как динамически изменять уровень обслуживания тома.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/12/2020
ms.author: b-juche
ms.openlocfilehash: e5219e1c87221ade8da68c21209f41b4d6139be2
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579085"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Динамическое изменение уровня обслуживания тома

> [!IMPORTANT] 
> * Регистрация в общедоступной предварительной версии для этой функции находится на удержании до дальнейшего уведомления. 
> * Динамическое изменение уровня обслуживания тома назначения репликации сейчас не поддерживается.

Уровень обслуживания существующего тома можно изменить, переместив том в другой пул мощностей, который использует требуемый [уровень обслуживания](azure-netapp-files-service-levels.md) для тома. Это изменение уровня обслуживания для тома выполняется на месте и не требует переноса данных. Он также не влияет на доступ к тому.  

Эта функция позволяет удовлетворить потребности вашей рабочей нагрузки по требованию.  Вы можете повысить уровень обслуживания тома для повышения производительности или использовать более низкий уровень обслуживания для оптимизации затрат. Например, если том находится в пуле емкости, который использует уровень обслуживания " *стандартный* " и вы хотите, чтобы этот том использовал уровень обслуживания " *премиум* ", вы можете динамически переместить том в пул емкости, использующий уровень обслуживания " *премиум* ".  

Пул емкости, в который нужно переместить том, должен уже существовать. Пул емкости может содержать другие тома.  Если вы хотите переместить том в новый пул ресурсов, необходимо [создать пул ресурсов](azure-netapp-files-set-up-capacity-pool.md) , прежде чем перемещать том.  

## <a name="considerations"></a>Рекомендации

* После перемещения тома в другой пул емкостью вы больше не сможете получить доступ к предыдущим журналам действий тома и метрикам тома. Том будет начинаться с новых журналов действий и метрик в новом пуле емкости.

* При перемещении тома в пул ресурсов более высокого уровня обслуживания (например, при переходе с уровня " *стандартный* " на уровень " *премиум* " или " *Ultra* Service") необходимо подождать не менее семи дней, прежде чем можно будет переместить *этот том в* пул емкости более низкого уровня обслуживания (например, переход с *Ultra* на *Premium* или *Standard* ).  
<!-- 
## Register the feature

The feature to move a volume to another capacity pool is currently in preview. If you are using this feature for the first time, you need to register the feature first.

1. Register the feature: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. Check the status of the feature registration: 

    > [!NOTE]
    > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is **Registered** before continuing.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
--> 
## <a name="move-a-volume-to-another-capacity-pool"></a>Перемещение тома в другой пул мощностей

1.  На странице тома щелкните правой кнопкой мыши том, уровень обслуживания которого необходимо изменить. Выберите **изменить пул**.

    ![Щелкните правой кнопкой мыши том](../media/azure-netapp-files/right-click-volume.png)

2. В окне Изменение пула выберите пул ресурсов, в который нужно переместить том. 

    ![Изменить пул](../media/azure-netapp-files/change-pool.png)

3.  Нажмите кнопку **ОК**.


## <a name="next-steps"></a>Следующие шаги  

* [Уровни обслуживания для Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Настройка пула емкости](azure-netapp-files-set-up-capacity-pool.md)
