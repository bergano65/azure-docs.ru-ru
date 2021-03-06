---
title: Руководство по цифровому центру дистрибуции для Интернета вещей | Документация Майкрософт
description: Руководство по шаблону приложения цифрового центра дистрибуции для IoT Central.
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 879e1911c715688c2fed4044e23f437b3bbef7b2
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890674"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Руководство по развертыванию шаблона приложения цифрового центра дистрибуции и его использованию

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

В этом руководстве показано, как приступить к работе, развернув шаблон приложения **цифрового центра дистрибуции** для IoT Central. Вы узнаете, как развернуть шаблон, который готов к использованию, и что можно будет сделать дальше.

Из этого руководства вы узнаете, как: 
* создать приложение цифрового центра дистрибуции; 
* ознакомиться с этим приложением. 

## <a name="prerequisites"></a>Предварительные требования
* Для развертывания этого приложения не требуется выполнять какие-либо предварительные требования.
* Рекомендуется иметь подписку Azure, но вы можете поработать с приложением даже без нее.

## <a name="create-digital-distribution-center-application-template"></a>Создание шаблона приложения цифрового центра дистрибуции

Вы можете создать приложение, выполнив следующие действия.

1. Перейдите на веб-сайт диспетчера приложений в Azure IoT Central. Выберите **Build** (Сборка) в области навигации слева, а затем перейдите на вкладку **Retail** (Розничная торговля).

> [!div class="mx-imgBorder"]
> ![Цифровой центр дистрибуции](./media/tutorial-iot-central-ddc/iotc-retail-homepage.png)

2. Выберите вкладку **Retail** (Розничная торговля) и щелкните **Create app** (Создать) в разделе **digital distribution center application** (Приложение цифрового центра дистрибуции).

3. Для **создания приложения** откроется форма нового приложения, которая будет заполнена запрошенными данными, как показано ниже.
   **Application name** (Имя приложения): можно использовать предложенное имя по умолчанию или ввести понятное имя приложения.
   **URL** (URL-адрес): можно использовать предложенный URL-адрес по умолчанию или ввести понятный уникальный URL-адрес. Если у вас уже есть подписка Azure, то рекомендуется использовать параметр по умолчанию. В противном случае вы можете начать с 7-дневной бесплатной пробной версии и затем перейти на оплату по мере использования в любое время до истечения срока действия бесплатной пробной версии.
   **Billing Info** (Данные для выставления счетов): сведения о каталоге, подписке Azure и регионе необходимы для подготовки ресурсов.
   **Создание**. Выберите "Create" (Создать) в нижней части страницы, чтобы развернуть приложение.

> [!div class="mx-imgBorder"]
> ![Цифровой центр дистрибуции](./media/tutorial-iot-central-ddc/ddc-create.png)

## <a name="walk-through-the-application-dashboard"></a>Знакомство с панелью мониторинга приложения 

После успешного развертывания шаблона приложения панель мониторинга по умолчанию является порталом оператора центра дистрибуции. Northwind Trader — это вымышленный поставщик решений центров дистрибуции, управляющий конвейерными системами. 

На этой панели мониторинга вы увидите один шлюз и одну камеру, действующую как устройство Интернета вещей. Шлюз предоставляет данные телеметрии о пакетах (допустимый, недопустимый или неидентифицированный пакет и его размер), а также связанные свойства двойника устройства. Все нисходящие команды выполняются на устройствах Интернета вещей, например на камере. Эта панель мониторинга предварительно настроена для демонстрации важных операций устройства центра дистрибуции.

Панель мониторинга логически организована, чтобы продемонстрировать возможности управления устройствами, обеспечиваемые шлюзом Azure IoT и устройством Интернета вещей.  
   * Вы можете выполнять команды шлюза и задачи управления.
   * Можно управлять всеми камерами, которые являются частью решения. 

> [!div class="mx-imgBorder"]
> ![Цифровой центр дистрибуции](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Шаблон устройства

Перейдите на вкладку "Device templates" (Шаблоны устройств), и вы увидите модель возможностей шлюза. Модель возможностей основана на двух интерфейсах, **Camera** (Камера) и **Digital Distribution Gateway** (Шлюз цифровой дистрибуции).

> [!div class="mx-imgBorder"]
> ![Цифровой центр дистрибуции](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**Camera** (Камера). Этот интерфейс организовывает все возможности команд камеры. 

> [!div class="mx-imgBorder"]
> ![Цифровой центр дистрибуции](./media/tutorial-iot-central-ddc/ddc-camera.png)

**Digital Distribution Gateway** (Шлюз цифровой дистрибуции). Этот интерфейс представляет все данные телеметрии, поступающие от камеры, определяемые облаком свойства двойника устройства и сведения о шлюзе.

> [!div class="mx-imgBorder"]
> ![Цифровой центр дистрибуции](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>"Gateway Commands" (Команды шлюза)
Этот интерфейс организовывает все возможности команд шлюза.

> [!div class="mx-imgBorder"]
> ![Цифровой центр дистрибуции](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Правила
Перейдите на вкладку "Rules" (Правила), чтобы просмотреть два правила в этом шаблоне приложения. Эти правила настроены для отправки уведомлений по электронной почте операторам для дальнейшего исследования.

 **Too many invalid packages alert** (Оповещение о слишком большом числе недопустимых пакетов). Это правило активируется, когда камера обнаруживает большое число недопустимых пакетов, передаваемых через конвейерную систему.
 
**Large package** (Большой пакет). Это правило активируется, если камера обнаруживает большой пакет, качество которого невозможно проверить. 

> [!div class="mx-imgBorder"]
> ![Цифровой центр дистрибуции](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Задания
Перейдите на вкладку "Jobs" (Задания), чтобы увидеть пять заданий, входящих в состав этого шаблона приложения. Функцию заданий можно использовать для выполнения операций уровня всего решения. Здесь задания центра дистрибуции используют команды устройства и возможности двойника для выполнения таких задач, как:
   * калибровка камеры перед началом обнаружения пакетов; 
   * периодическое обновление встроенного ПО камеры;
   * изменение интервала отправки данных телеметрии для управления передачей данных.

> [!div class="mx-imgBorder"]
> ![Цифровой центр дистрибуции](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Очистка ресурсов
Если вы не собираетесь в дальнейшем использовать это приложение, удалите шаблон приложения, выбрав **Administration** (Администрирование)  >  **Application settings** (Параметры приложения) и щелкнув **Delete** (Удалить).

> [!div class="mx-imgBorder"]
> ![Цифровой центр дистрибуции](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Дополнительная информация
* Узнайте больше об архитектуре решения цифрового центра дистрибуции и [его принципах действия](./architecture-digital-distribution-center-pnp.md).
* Узнайте больше о других [шаблонах IoT Central для розничной торговли](./overview-iot-central-retail-pnp.md).
* См. сведения об [IoT Central](../preview/overview-iot-central.md).
