---
title: Управление приложением IoT Central Azure | Документация Майкрософт
description: Как администратор, как управлять приложением IoT Central Azure, изменяя имя приложения, URL-адрес, передачу образа, копирование и удаление приложения.
author: viv-liu
ms.author: viviali
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: bdb13ef0d4f05802e4ab0f383c1bf3d82d9ba632
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880973"
---
# <a name="manage-your-iot-central-application"></a>Управление приложением IoT Central

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

В этой статье описывается, как администратор может управлять приложением, изменяя имя приложения и URL-адрес, отправку образа, а также можете узнать, как копировать и удалять приложение в приложении IoT Central Azure.

Чтобы получить доступ к разделу **Администрирование**, вам должна быть назначена роль **Администратор** приложения Azure IoT Central. Если вы создаете приложение Azure IoT Central, вам автоматически назначается роль **Администратор** для этого приложения. 

## <a name="change-application-name-and-url"></a>Изменение имени приложения и URL-адреса

На странице **Параметры приложения** можно изменить имя и URL-адрес приложения, а затем выбрать **Сохранить**.

![Страница "Параметры приложения"](media/howto-administer-pnp/image0-a.png)

Если администратор создает пользовательскую тему для приложения, эта страница содержит параметр, позволяющий скрыть **имя приложения** в пользовательском интерфейсе. Это полезно, если эмблема приложения в пользовательской теме содержит имя приложения. Дополнительные сведения см. [в статье Настройка пользовательского интерфейса IOT Central Azure](./howto-customize-ui-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

> [!Note]
> Если URL-адрес будет изменен, старый URL-адрес может быть занят другим клиентом Azure IoT Central. В этом случае он будет недоступен для использования. При изменении URL-адреса старый URL-адрес больше нельзя будет использовать, поэтому вам нужно уведомить пользователей о новом URL-адресе.

## <a name="prepare-and-upload-image"></a>Подготовка и передача изображения

Чтобы изменить изображение приложения, ознакомьтесь со статьей [Подготовка и передача изображений в приложение Azure IoT Central](howto-prepare-images.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="copy-an-application"></a>Копирование приложения

Можно создать копию любого приложения без экземпляров устройства, журнала данных устройства и данных пользователя. Копия — это приложение с оплатой по мере использования, за которое взимается плата. Таким образом, нельзя создать бесплатную пробную версию приложения.

Выберите **Копировать**. В диалоговом окне введите сведения для нового приложения с оплатой по мере использования. Затем выберите **Копировать** , чтобы подтвердить продолжение работы. Дополнительные сведения о полях в этой форме см. в руководстве [Создание приложения Azure IoT Central](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

![Страница "Параметры приложения"](media/howto-administer-pnp/appcopy2.png)

После завершения операции копирования приложения можно переходить к новому приложению, используя ссылку.

![Страница "Параметры приложения"](media/howto-administer-pnp/appcopy3a.png)

При копировании приложения также копируются определение правил и действий по электронной почте. Некоторые действия, например Flow, Logic Apps и т. д., привязаны к определенным правилам с помощью идентификатора правила. Когда правило копируется в другое приложение, оно получает собственный идентификатор правила. В этом случае пользователям придется создать новое действие, а затем связать с ним новое правило. Как правило, рекомендуется проверять правила и действия, чтобы убедиться, что они обновлены в новом приложении.

> [!WARNING]
> Если на панели мониторинга есть плитки, отображающие сведения о конкретных устройствах, то запрошенный **ресурс не был найден** в новом приложении. Необходимо перенастроить эти плитки, чтобы отобразить сведения об устройствах в новом приложении.

## <a name="delete-an-application"></a>Удаление приложения

Нажмите кнопку **Удалить**, чтобы окончательно удалить приложение IoT Central. Это действие окончательно удаляет все данные, связанные с приложением.

> [!Note]
> Чтобы удалить приложение, у вас также должны быть разрешения на удаление ресурсов в подписке Azure, которую вы выбрали при создании приложения. Дополнительные сведения см. в статье [Использование управления доступом на основе ролей для контроля доступа к ресурсам в подписке Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).


## <a name="manage-programatically"></a>Программное управление

Пакеты SDK IoT Central Azure Resource Manager доступны для Node, Python, C#, Ruby, Java и Go. Эти пакеты можно использовать для создания, перечисления, обновления или удаления IoT Central приложений. Пакеты содержат вспомогательные методы для управления проверкой подлинности и обработкой ошибок.

Примеры использования пакетов SDK для Azure Resource Manager см. здесь: [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Дополнительные сведения см. в следующих репозиториях и пакетах GitHub:

| Язык | Репозиторий | Пакет |
| ---------| ---------- | ------- |
| Узел | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Вперед | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Следующие шаги
 
Теперь, когда вы узнали, как администрировать приложение Azure IoT Central, предлагаем следующий шаг: Узнайте, как [управлять пользователями и ролями](howto-manage-users-roles-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) в Azure IOT Central.