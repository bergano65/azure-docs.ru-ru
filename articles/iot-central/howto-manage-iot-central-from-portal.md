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
ms.openlocfilehash: 89109dec83342a8f4b5962778b1803eb36656e42
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352224"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Управление IoT Central на портале Azure

Вместо создания приложений IoT Central и управления ими со страницы [диспетчера приложений](https://aka.ms/iotcentral) IoT Central вы можете использовать [портал Azure](https://portal.azure.com), чтобы управлять ими.

## <a name="create-iot-central-applications"></a>Создание приложений IoT Central

Чтобы создать приложение, перейдите на [портал Azure](https://ms.portal.azure.com) и щелкните **Создать ресурс** в главном меню навигации слева.

![Портал управления: меню навигации](media/howto-manage-iot-central-from-portal/image0.png)

В строке поиска введите **IoT Central**.

![Портал управления: поиск](media/howto-manage-iot-central-from-portal/image0a.png)

В результатах поиска щелкните пункт **IoT Central Application**.

![Портал управления: результаты поиска](media/howto-manage-iot-central-from-portal/image0b.png)

Теперь нажмите кнопку **Создать**.

![Портал управления. Ресурс IoT Central](media/howto-manage-iot-central-from-portal/image0c.png)

Заполните все поля в форме. Эта форма похожа на ту, которую необходимо заполнить при создании приложения на странице [Диспетчер приложений](https://aka.ms/iotcentral) IoT Central. Дополнительные сведения см. в кратком руководстве [Создание приложения Azure IoT Central](quick-deploy-iot-central.md).

![Портал управления: создание ресурса IoT Central](media/howto-manage-iot-central-from-portal/image1.png)  

Заполнив все поля, нажмите кнопку **Создать**.

## <a name="manage-existing-iot-central-applications"></a>Управление существующими приложениями IoT Central

Если у вас уже есть приложение IoT Central, его можно удалить, а также перенести в другую подписку или группу ресурсов на портале Azure.

> [!NOTE]
> Пробная версия приложений не отображается на портале Azure, поскольку они не связаны с вашей подпиской.

Сначала выберите пункт **Все ресурсы** в главном меню навигации слева. В поле поиска введите имя приложения, чтобы найти его в списке ресурсов. Затем щелкните приложение IoT Central, которым необходимо управлять.

![Портал управления: управление ресурсами](media/howto-manage-iot-central-from-portal/image2.png)

Чтобы перейти к приложению, щелкните URL-адрес приложения IoT Central.

![Портал управления: управление ресурсами](media/howto-manage-iot-central-from-portal/image3.png)

Чтобы перенести приложение в другую группу ресурсов, щелкните **Изменить** рядом с группой ресурсов. На странице **Перемещение ресурсов** выберите группу ресурсов, в которую необходимо перенести это приложение.

![Портал управления: управление ресурсами](media/howto-manage-iot-central-from-portal/image4.png)

Чтобы перенести приложение в другую подписку, щелкните ссылку **Изменить** рядом с подпиской. В появившемся диалоговом окне выберите подписку, в которую необходимо перенести это приложение.

![Портал управления: управление ресурсами](media/howto-manage-iot-central-from-portal/image5.png)

## <a name="next-steps"></a>Дополнительная информация

Вы узнали, как управлять приложениями Azure IoT Central на портале Azure, а значит, вы готовы к следующему шагу:

> [!div class="nextstepaction"]
> [Администрирование приложения](howto-administer.md)