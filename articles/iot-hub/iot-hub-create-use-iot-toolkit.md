---
title: Создание Центра Интернета вещей Azure с помощью средств Интернета вещей Azure для VS Code | Документация Майкрософт
description: Узнайте, как использовать средства Azure IoT для Visual Studio Code, чтобы создать центр Интернета вещей Azure в группе ресурсов.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 3fe4dfae2e86d549056805e87c0fd3927697fe24
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890568"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Создание Центра Интернета вещей с помощью средств Интернета вещей Azure для Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

В этой статье показано, как с помощью [Средства Azure IoT для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) создать Центр Интернета вещей Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Для работы с этой статьей необходимо следующее:

- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , прежде чем начинать работу.

- [Visual Studio Code](https://code.visualstudio.com/)

- [Средства Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) для Visual Studio Code.

## <a name="create-an-iot-hub"></a>Создание центра IoT

1. Откройте в Visual Studio Code представление **Explorer**.

2. В нижней части Explorer разверните раздел **Устройства Центра Интернета вещей Azure**. 

   ![Развернутый раздел "Устройства Центра Интернета вещей Azure"](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Щелкните значок **...** в заголовке раздела **Устройства Центра Интернета вещей Azure**. Если значок многоточия не отображается, наведите курсор на заголовок. 

4. Выберите **Создать центр IoT**.

5. При первом входе в Azure в правом нижнем углу появится всплывающее окно.

6. Выберите подписку Azure. 

7. Выберите группу ресурсов.

8. Выберите расположение.

9. Выберите ценовой уровень.

10. Введите глобальное уникальное имя вашего Центра Интернета вещей.

11. Подождите несколько минут, пока Центр Интернета вещей не будет создан.

## <a name="next-steps"></a>Дополнительная информация

Вы развернули Центр Интернета вещей с помощью средств Интернета вещей Azure для Visual Studio Code. Чтобы продолжить изучение, ознакомьтесь со следующими статьями:

* [Обмен сообщениями между устройством и Центром Интернета вещей с помощью расширения набора средств Центра Интернета вещей Azure для Visual Studio Code](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Управление устройствами Интернета вещей Azure с помощью расширения "Набор средств Центра Интернета вещей Azure" для Visual Studio Code](iot-hub-device-management-iot-toolkit.md)

* [Вики-страница, посвященная набору средств Интернета вещей Azure](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki)
