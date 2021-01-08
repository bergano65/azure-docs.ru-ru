---
title: Замена устройства Azure Stack пограничной Pro | Документация Майкрософт
description: Описывает, как получить замену Azure Stack устройстве Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: 501136c6f5015dc8c68a1aba004432900675b11c
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017975"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>Замена устройства Azure Stack пограничной Pro

В этой статье описывается, как получить заменяющее устройство Pro Azure Stack. Устройство на замену необходимо, если на имеющемся устройстве произошел сбой оборудования или требуется обновление. 


Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
>
> * Откройте запрос в службу поддержки для устранения проблем с оборудованием
> * Создание нового ресурса для замены устройства в портал Azure
> * Установка, активация устройства замены
> * Возврат исходного устройства

## <a name="open-a-support-ticket"></a>Создание запроса в службу поддержки

Если на имеющемся устройстве произошел сбой оборудования, отправьте запрос в службу поддержки. Служба поддержки Майкрософт определит, что компоненты для замены на месте (FRU) недоступен для этого экземпляра или требуется обновление оборудования. В обоих случаях служба поддержки закажет устройство на замену.

1. Откройте запрос в службу поддержки Майкрософт и укажите, что хотите вернуть устройство. Выберите тип проблемы **Azure Stack пограничная оборудование Pro**.

    ![Открыть запрос в службу поддержки](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Сотрудник службы поддержки Майкрософт свяжется с вами. Укажите сведения о доставке.
<!--3. If you need a return shipping box, you can request it. Answer **Yes** to the question **Need an empty box to return**.-->


## <a name="create-a-resource-for-replacement-device"></a>Создание ресурса для замены устройства

> [!NOTE]
> Создайте новый ресурс для активации заменяющего устройства. Активация замены устройства на существующий ресурс не поддерживается.

Чтобы создать ресурс, выполните следующие действия.

1. Выполните действия, описанные в разделе [Создание нового ресурса](azure-stack-edge-deploy-prep.md#create-a-new-resource) , чтобы создать ресурс для устройства замены. 

2. Убедитесь, что установлен флажок **у меня есть устройство Azure Stack пограничной Pro**. 

    ![Ресурс для замены устройства](media/azure-stack-edge-replace-device/replace-resource-1.png)  

## <a name="install-and-activate-the-replacement-device"></a>Установка и активация устройства замены

Выполните следующие действия, чтобы установить и активировать устройство замены.

1. [Установите устройство](azure-stack-edge-deploy-install.md).

2. [Активируйте устройство](azure-stack-edge-deploy-connect-setup-activate.md) для нового ресурса, созданного ранее.

## <a name="return-your-existing-device"></a>Возврат существующего устройства

Выполните все действия, чтобы вернуть исходное устройство:

1. [Сотрите данные с устройства](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. [Инициация возврата устройства](azure-stack-edge-return-device.md#initiate-device-return) для исходного устройства.
3. [Вызовите курьера](azure-stack-edge-return-device.md#schedule-a-pickup).
4. [Удалите ресурс](azure-stack-edge-return-device.md#delete-the-resource), связанный с возвращаемым устройством.


## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [вернуть устройство Pro Azure Stack](azure-stack-edge-return-device.md).
