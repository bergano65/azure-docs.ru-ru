---
title: Управление IoT Central на портале Azure | Документы Майкрософт
description: Управление IoT Central на портале Azure
services: iot-central
ms.service: iot-central
author: tbhagwat3
ms.author: tanmayb
ms.date: 08/30/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: ff6978ddbf8718ad45a4265898109b7ac799d70c
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46468619"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Управление IoT Central на портале Azure 
Помимо создания приложений IoT Central и управления ими на веб-сайте IoT Central, решением IoT Central можно также управлять на портале Azure. В этой статье разбираются возможные задачи и способ их выполнения.

## <a name="create-iot-central-applications"></a>Создание приложений IoT Central
Чтобы создать приложение, перейдите на [портал Azure](https://ms.portal.azure.com) и щелкните "Создать ресурс" в главном меню навигации слева. 

![Портал управления: меню навигации](media\howto-manage-iot-central-from-portal\image0.png)

В строке поиска введите запрос "IoT Central".

![Портал управления: поиск](media\howto-manage-iot-central-from-portal\image0a.png)

В результатах поиска щелкните пункт "Приложение IoT Central".

![Портал управления: результаты поиска](media\howto-manage-iot-central-from-portal\image0b.png)

Далее нажмите кнопку "Создать", чтобы открыть форму, которую необходимо заполнить.

![Портал управления: ресурс IoT Central](media\howto-manage-iot-central-from-portal\image0c.png)

Заполните все поля в форме. Эта форма похожа на ту, которую необходимо заполнить при создании приложения на веб-сайте IoT Central. Дополнительные сведения о заполнении каждого поля см. в документе [Создание приложения IoT Central](https://docs.microsoft.com/ azure/iot-central/howto-create-application). 

![Портал управления: создание ресурса IoT Central](media\howto-manage-iot-central-from-portal\image1.png)  

Заполнив все поля, нажмите кнопку "Создать".

## <a name="manage-existing-iot-central-applications"></a>Управление существующими приложениями IoT Central
Если у вас уже есть приложение IoT Central, его можно удалить, а также перенести в другую подписку или группу ресурсов на портале Azure. Пробные 7-дневные приложения не отображаются на портале Azure, так как они не связаны с подпиской.

Сначала выберите пункт "Все ресурсы" в главном меню навигации слева. В поле поиска введите имя приложения и найдите его в списке ресурсов. Затем щелкните приложение IoT Central, которым необходимо управлять.

![Портал управления: управление ресурсами](media\howto-manage-iot-central-from-portal\image2.png)

Чтобы перейти к приложению, щелкните URL-адрес приложения IoT Central.

![Портал управления: управление ресурсами](media\howto-manage-iot-central-from-portal\image3.png)

Чтобы перенести приложение в другую группу ресурсов, щелкните ссылку **изменить** рядом с группой ресурсов. В появившемся диалоговом окне выберите группу ресурсов, в которую необходимо перенести это приложение.

![Портал управления: управление ресурсами](media\howto-manage-iot-central-from-portal\image4.png)

Чтобы перенести приложение в другую подписку, щелкните ссылку **изменить** рядом с подпиской. В появившемся диалоговом окне выберите подписку, в которую необходимо перенести это приложение.

![Портал управления: управление ресурсами](media\howto-manage-iot-central-from-portal\image5.png)

## <a name="next-steps"></a>Дополнительная информация

Вы узнали, как управлять приложениями Azure IoT Central на портале Azure, а значит, вы готовы к следующему шагу:

> [!div class="nextstepaction"]
> [Администрирование приложения](howto-administer.md)