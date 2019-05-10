---
title: Управление IoT Central на портале Azure | Документы Майкрософт
description: Управление IoT Central на портале Azure
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/14/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: c72de0ef874659a5d7840689e38bd7857c25b840
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464114"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Управление IoT Central на портале Azure

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Вместо создания приложений IoT Central и управления ими со страницы [диспетчера приложений](https://aka.ms/iotcentral) IoT Central вы можете использовать [портал Azure](https://portal.azure.com), чтобы управлять ими.

## <a name="create-iot-central-applications"></a>Создание приложений IoT Central

Чтобы создать приложение, перейдите к [портала Azure](https://ms.portal.azure.com) и выберите **создать ресурс** в основное меню навигации слева.

![Портал управления: меню навигации](media/howto-manage-iot-central-from-portal/image0.png)

В строке поиска введите **IoT Central**.

![Портал управления: поиск](media/howto-manage-iot-central-from-portal/image0a1.png)

Выберите **приложение центра Интернета вещей** элемента строки в результатах поиска.

![Портал управления: результаты поиска](media/howto-manage-iot-central-from-portal/image0b1.png)

Выберите **Создать**.

![Портал управления. Ресурс IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Заполните все поля в форме. Эта форма похожа на ту, которую необходимо заполнить при создании приложения на странице [Диспетчер приложений](https://aka.ms/iotcentral) IoT Central. Дополнительные сведения см. в кратком руководстве [Создание приложения Azure IoT Central](quick-deploy-iot-central.md).

![Портал управления: создание ресурса IoT Central](media/howto-manage-iot-central-from-portal/image1a.png)  

После заполнения всех полей, выберите **создать**.

## <a name="manage-existing-iot-central-applications"></a>Управление существующими приложениями IoT Central

Если у вас уже есть приложение IoT Central, его можно удалить, а также перенести в другую подписку или группу ресурсов на портале Azure.

> [!NOTE]
> Пробная версия приложений не отображается на портале Azure, поскольку они не связаны с вашей подпиской.

Чтобы приступить к работе, выберите **все ресурсы** в основное меню навигации слева. В поле поиска введите имя приложения, чтобы найти его в списке ресурсов. Затем выберите приложение IoT Central, которые вы хотите управлять.

![Портал управления: управление ресурсами](media/howto-manage-iot-central-from-portal/image2a.png)

Чтобы перейти к приложению, выберите URL-адрес центра приложений Интернета вещей.

![Портал управления: управление ресурсами](media/howto-manage-iot-central-from-portal/image3.png)

Чтобы переместить приложение в другую группу ресурсов, выберите **изменить** рядом с группой ресурсов. На странице **Перемещение ресурсов** выберите группу ресурсов, в которую необходимо перенести это приложение.

![Портал управления: управление ресурсами](media/howto-manage-iot-central-from-portal/image4a.png)

Чтобы переместить приложение в другую подписку, выберите **изменить** ссылку рядом с подпиской. В появившемся диалоговом окне выберите подписку, в которую необходимо перенести это приложение.

![Портал управления: управление ресурсами](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Дальнейшие действия

Вы узнали, как управлять приложениями Azure IoT Central на портале Azure, а значит, вы готовы к следующему шагу:

> [!div class="nextstepaction"]
> [Администрирование приложения](howto-administer.md)