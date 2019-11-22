---
title: Управление IoT Central на портале Azure | Документы Майкрософт
description: В этой статье описывается создание приложений IoT Central и управление ими из портал Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/02/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: c9a8ed6d55fbbfcf59c98a86ac98cb4264d31ee4
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278817"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Управление IoT Central на портале Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Вместо создания приложений IoT Central и управления ими на веб-сайте [диспетчера приложений Azure IOT Central](https://aka.ms/iotcentral) можно использовать [портал Azure](https://portal.azure.com) для управления приложениями.

## <a name="create-iot-central-applications"></a>Создание приложений IoT Central

Чтобы создать приложение, перейдите к [портал Azure](https://ms.portal.azure.com) и выберите **создать ресурс** на главной панели слева.

![Портал управления: меню навигации](media/howto-manage-iot-central-from-portal/image0.png)

В строке поиска введите **IoT Central**.

![Портал управления: поиск](media/howto-manage-iot-central-from-portal/image0a1.png)

В результатах поиска выберите **IOT Central приложение** — элемент.

![Портал управления: результаты поиска](media/howto-manage-iot-central-from-portal/image0b1.png)

Выберите **Создать**.

![Портал управления: ресурс IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Заполните все поля в форме. Эта форма аналогична форме, которую вы заполнили для создания приложений на веб-сайте [Azure IOT Central Application Manager](https://aka.ms/iotcentral) . Дополнительные сведения см. в кратком руководстве [Создание приложения Azure IoT Central](quick-deploy-iot-central.md).

**Расположение** — это [География](https://azure.microsoft.com/global-infrastructure/geographies/) , где вы хотите создать приложение. Как правило, для достижения оптимальной производительности следует выбирать расположение, расположенное рядом с вашими устройствами. Azure IoT Central в настоящее время доступна в **США**, **Австралии**, **Азиатско-Тихоокеанский регион**или в **Европе**.  После выбора расположения вы не сможете переместить приложение в другое расположение позже.

> [!NOTE]
> Шаблон **предварительной версии приложения** доступен только в **странах Европы** и **США** .

![Портал управления: создание ресурса IoT Central](media/howto-manage-iot-central-from-portal/image1a.png)  

После заполнения всех полей выберите **создать**.

## <a name="manage-existing-iot-central-applications"></a>Управление существующими приложениями IoT Central

Если у вас уже есть приложение IoT Central, его можно удалить, а также перенести в другую подписку или группу ресурсов на портале Azure.

> [!NOTE]
> Пробная версия приложений не отображается на портале Azure, поскольку они не связаны с вашей подпиской.

Чтобы начать работу, выберите **все ресурсы** на главной панели слева. В поле поиска введите имя приложения, чтобы найти его в списке ресурсов. Затем выберите приложение IoT Central, которым вы хотите управлять.

![Портал управления: управление ресурсами](media/howto-manage-iot-central-from-portal/image2a.png)

Для перехода к приложению выберите IoT Central URL-адрес приложения.

![Портал управления: управление ресурсами](media/howto-manage-iot-central-from-portal/image3.png)

Чтобы переместить приложение в другую группу ресурсов, выберите **изменить** рядом с группой ресурсов. На странице **Перемещение ресурсов** выберите группу ресурсов, в которую необходимо перенести это приложение.

![Портал управления: управление ресурсами](media/howto-manage-iot-central-from-portal/image4a.png)

Чтобы переместить приложение в другую подписку, щелкните ссылку **изменить** рядом с подпиской. В появившемся диалоговом окне выберите подписку, в которую необходимо перенести это приложение.

![Портал управления: управление ресурсами](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Дополнительная информация

Вы узнали, как управлять приложениями Azure IoT Central на портале Azure, а значит, вы готовы к следующему шагу:

> [!div class="nextstepaction"]
> [Администрирование приложения](howto-administer.md)