---
title: Create and manage Azure IoT Central applications from the CSP portal | Microsoft Docs
description: Сведения о создании приложения Azure IoT Central поставщиком облачных решений от имени клиента.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 1588a27453dbc223e6aa1dd993905d274e5e1242
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480335"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Create and manage an Azure IoT Central application from the CSP portal

Программа для поставщиков облачных решений (CSP) (Microsoft) — это программа Microsoft Reseller. Ее целью является предоставление партнерам Channel Partner единого решения для продажи всех коммерческих служб Microsoft Online Services. Узнайте больше о [программе для поставщиков облачных решений](https://partner.microsoft.com/cloud-solution-provider).

Поставщик облачных решений может создавать решения Microsoft Azure IoT Central и управлять ими от имени клиентов с помощью [Центра партнеров Майкрософт](https://partnercenter.microsoft.com/partner/home). Если поставщик облачных решений создает приложения Azure IoT Central от имени клиентов так же, как другие управляемые им службы Azure, он сам занимается процедурой управления выставлением счетов для клиентов. Плата за Azure IoT Central будет отображаться в общем счете в Центре партнеров Майкрософт.

Чтобы приступить к работе, войдите в учетную запись на портале партнеров Майкрософт и выберите клиента, для которого вы хотите создать приложение Azure IoT Central. Navigate to Service Management for the customer from the left nav.

![Клиентское представление в Центре партнеров Майкрософт](media/howto-create-application-csp/image1.png)

Azure IoT Central отображается как служба, доступная для администрирования. Select the Azure IoT Central link on the page to create new applications or manage existing applications for this customer.

![Доступная для управления служба Azure IoT Central](media/howto-create-application-csp/image2.png)

Откроется страница диспетчера приложений в Azure IoT Central. Azure IoT Central сохраняет контекст, указывающий, что вы перешли из Центра партнеров Майкрософт и планируете выполнять управление для этого конкретного клиента. Эти сведения отображаются в заголовке страницы диспетчера приложений. Отсюда можно перейти к существующему приложению, которое вы создали ранее для этого клиента, чтобы управлять им, или можно создать новое приложение для клиента.

![Создание диспетчера для поставщиков облачных решений](media/howto-create-application-csp/image3.png)

To create an Azure IoT Central application, select **Build** in the left menu. Choose one of the industry templates, or choose **Custom app** to create an application from scratch. Будет загружена страница создания приложения. Необходимо заполнить все поля на этой странице и нажать кнопку **Создать**. Ниже приводятся сведения о каждом поле.

![Страница создания приложения для CSP](media/howto-create-application-csp/image4.png)

![Страница создания приложения для CSP](media/howto-create-application-csp/image4-1.png)

## <a name="payment-plan"></a>План оплаты

Поставщик CSP может создавать только приложения с оплатой по мере использования. Чтобы продемонстрировать Azure IoT Central клиенту, отдельно создайте пробную версию приложения. Дополнительные сведения о пробных версиях приложений и версий с оплатой по мере использования см. [на странице с ценами на Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Имя приложения

Имя приложения отображается на странице **диспетчера приложений** и в пределах каждого приложения Azure IoT Central. Приложению можно присвоить любое имя. Выберите имя, которое является понятным для вас и других участников организации.

## <a name="application-url"></a>URL-адрес приложения

URL-адрес приложения представляет собой ссылку на приложение. Вы можете сохранить закладку со ссылкой в браузере или поделиться URL-адресом с другими участниками.

URL-адрес приложения будет создан автоматически, когда вы введете имя приложения. При желании вы можете выбрать другой URL-адрес. URL-адрес каждого приложения Azure IoT Central должен быть уникальным в Azure IoT Central. Если выбрать URL-адрес, который уже используется, отобразится сообщение об ошибке.

## <a name="directory"></a>Каталог

Так как Azure IoT Central сохраняет контекст, говорящий о ваших планах по управлению клиентом, выбранным на портале партнеров Майкрософт, в поле "Каталог" вы увидите только клиент Azure Active Directory для этого клиента. 

Клиент Azure Active Directory содержит удостоверения пользователей, учетные данные и другие сведения об организации. Несколько подписок Azure можно связать с одним клиентом Azure Active Directory.

Дополнительные сведения см. в [документации по Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Подписка на Azure

Подписка Azure позволяет создавать экземпляры служб Azure. Azure IoT Central автоматически обнаруживает все подписки Azure клиента, к которым у вас есть доступ, и отображает их в раскрывающемся списке на странице **Создание приложения**. Выберите подписку Azure, чтобы создать приложение Azure IoT Central.

Если у вас еще нет подписки Azure, создайте ее в Центре партнеров Майкрософт. После создания подписки Azure перейдите на страницу **Создание приложения**. В раскрывающемся списке **Подписка Azure** отобразится новая подписка.

Дополнительные сведения см. в разделе о [подписках Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Регион

Choose the region or [geography](https://azure.microsoft.com/global-infrastructure/geographies/) where you’d like to create your Azure IoT Central Application. Typically, you should choose the region that's closest physically to your devices to get optimal performance.

> [!NOTE]
> The preview application templates are currently only available in the **Europe** and **US** locations.

To learn more, see [Azure regions](https://azure.microsoft.com/global-infrastructure/regions/) and [Azure geographies](https://azure.microsoft.com/global-infrastructure/geographies/).

Регионы, в которых доступно приложение Azure IoT Central,можно просмотреть на странице [Доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central).

> [!Note]
> Выбрав регион, вы не можете переместить приложение в другой регион.

## <a name="application-template"></a>Шаблон приложения

Вы можете выбрать один из доступных шаблонов приложений для нового приложения Azure IoT Central. Шаблон приложения может содержать стандартные элементы (например, шаблоны устройств и панели мониторинга), которые позволяют вам приступить к работе.

| Шаблон приложения | Описание |
| -------------------- | ----------- |
| Custom application (Пользовательское приложение)   | Создает пустое приложение, в которое необходимо добавить собственные шаблоны устройств и сами устройства. |
| Sample Contoso (Образец Contoso)       | Создает приложение, которое содержит шаблон устройства для легко подключаемого устройства. Используйте этот шаблон, чтобы приступить к работе в Azure IoT Central. |
| Custom application (Образец Devkits)       | Создает приложение с готовыми шаблонами устройств, чтобы вы могли подключить устройство MXChip или Raspberry Pi. Используйте этот шаблон, если вы являетесь разработчиком устройства, работающим с кодом в одном из этих устройств. |

## <a name="next-steps"></a>Дальнейшие действия

Вы узнали, как создать приложение Azure IoT Central в качестве поставщика облачных решений, а значит, вы готовы к следующему шагу.

> [!div class="nextstepaction"]
> [Администрирование приложения](howto-administer.md)
