---
title: Управление IoT Central на портале Azure | Документы Майкрософт
description: В этой статье описывается, как создавать и управлять приложениями IoT Central с портала Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: e7efda8efa27044168386e3ebbc557bf7fb74e8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157931"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Управление IoT Central на портале Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Вместо создания и управления приложениями IoT Central на [веб-сайте менеджера приложений Azure IoT](https://aka.ms/iotcentral) можно использовать [портал Azure](https://portal.azure.com) для управления приложениями.

## <a name="create-iot-central-applications"></a>Создание приложений IoT Central

Чтобы создать приложение, перейдите на [портал Azure](https://ms.portal.azure.com) и выберите **Создать ресурс.**

В **поиске** панели Marketplace введите *IoT Central:*

![Портал управления: поиск](media/howto-manage-iot-central-from-portal/image0a1.png)

Выберите плитку **Центрального приложения IoT** в результатах поиска:

![Портал управления: результаты поиска](media/howto-manage-iot-central-from-portal/image0b1.png)

Теперь выберите **Создать:**

![Портал управления: ресурс IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Заполните все поля в форме. Эта форма аналогична форме, заполненной для создания приложений на веб-сайте [менеджера приложений Azure IoT Central.](https://aka.ms/iotcentral) Дополнительные сведения см. в кратком руководстве [Создание приложения Azure IoT Central](quick-deploy-iot-central.md).

![Создание центральной формы IoT](media/howto-manage-iot-central-from-portal/image6a.png)

**Расположение** — [географический регион](https://azure.microsoft.com/global-infrastructure/geographies/), где вы хотите создать приложение. Как правило, следует выбирать расположение как можно ближе к устройствам, чтобы обеспечить оптимальную производительность. Azure IoT Central в настоящее время доступна в **Австралии,** **Азиатско-Тихоокеанском регионе,** **Европе,** **США,** **Великобритании**и **Японии.** Выбрав расположение, вы не сможете переместить приложение в другое расположение.

После заполнения всех полей, выберите **Создать**.

## <a name="manage-existing-iot-central-applications"></a>Управление существующими приложениями IoT Central

Если у вас уже есть приложение IoT Central, его можно удалить, а также перенести в другую подписку или группу ресурсов на портале Azure.

> [!NOTE]
> Вы не можете видеть приложения, созданные в плане бесплатного ценообразования, на портале Azure, поскольку они не связаны с подпиской.

Для начала выберите **все ресурсы** на портале. Выберите **Показать скрытые типы** и начать вводить имя приложения в **фильтр по имени,** чтобы найти его. Затем выберите приложение IoT Central, которым вы хотите управлять.

Чтобы перейти к приложению, выберите **URL-адрес центрального приложения IoT:**

![Портал управления: управление ресурсами](media/howto-manage-iot-central-from-portal/image3.png)

Чтобы переместить приложение в другую группу ресурсов, выберите **изменение** рядом с группой ресурсов. На странице **ресурсов Move** выберите группу ресурсов, которая, по множам, должна переместить это приложение в:

![Портал управления: управление ресурсами](media/howto-manage-iot-central-from-portal/image4a.png)

Чтобы переместить приложение в другую подписку, выберите **изменение** рядом с подпиской. На странице **ресурсов Move** выберите подписку, в которая вы хотите переместить это приложение:

![Портал управления: управление ресурсами](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Дальнейшие действия

Вы узнали, как управлять приложениями Azure IoT Central на портале Azure, а значит, вы готовы к следующему шагу:

> [!div class="nextstepaction"]
> [Администрирование приложения](howto-administer.md)