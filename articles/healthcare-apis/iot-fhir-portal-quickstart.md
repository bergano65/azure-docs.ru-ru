---
title: Краткое руководство. Развертывание соединителя IoT (предварительная версия) с помощью портала Azure
description: В этом кратком руководстве показано, как развернуть, настроить и использовать функцию соединителя IoT в Azure API для FHIR с помощью портала Azure.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.date: 05/11/2020
ms.author: punagpal
ms.openlocfilehash: 95f5b5f13401c224ccf67c5f013deedf00379de7
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446810"
---
# <a name="quickstart-deploy-iot-connector-preview-using-azure-portal"></a>Краткое руководство. Развертывание соединителя IoT (предварительная версия) с помощью портала Azure

Соединитель IoT — это дополнительная функция Azure API для FHIR, которая позволяет принимать данные из Интернета медицинских вещей (Internet of Medical Things, IoMT). На этапе предварительной версии функция соединителя IoT доступна бесплатно. В этом кратком руководстве описано следующее:
- Развертывание и настройка соединителя IoT с помощью портала Azure.
- Использование имитированного устройства для отправки данных в соединитель IoT.
- Просмотр ресурсов, созданных соединителем IoT в Azure API для FHIR.

## <a name="prerequisites"></a>Предварительные требования

- Активная подписка Azure — [создайте подписку бесплатно](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ресурс Azure API для FHIR — [разверните Azure API для FHIR с помощью портала Azure](fhir-paas-portal-quickstart.md).

## <a name="go-to-azure-api-for-fhir-resource"></a>Переход к ресурсу Azure API для FHIR.

Откройте [портал Azure](https://portal.azure.com) и перейдите к ресурсу **Azure API для FHIR**, для которого вы хотите создать функцию соединителя IoT.

[![Ресурс Azure API для FHIR](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg)](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg#lightbox)

В меню навигации слева щелкните **соединитель IoT (предварительная версия)** в разделе **Надстройки**, чтобы открыть страницу **Соединители IoT**.

[![Функция соединителя IoT](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg#lightbox)

## <a name="create-new-iot-connector-preview"></a>Создание соединителя IoT (предварительная версия)

Нажмите кнопку **Добавить**, чтобы открыть страницу **создания соединителя IoT**.

[![Добавление соединителя IoT](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg#lightbox)

Введите параметры нового соединителя IoT. Нажмите кнопку **Создать** и дождитесь развертывания соединителя IoT.

> [!NOTE]
> Выберите **Создать** в качестве значения в раскрывающемся списке **Тип разрешения** для этой установки. 

[![Создание соединителя IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg#lightbox)

|Параметр|Значение|Описание |
|---|---|---|
|Имя соединителя|Уникальное имя|Введите имя, чтобы определить соединитель IoT. Это имя должно быть уникальным в пределах ресурса Azure API для FHIR. Имя может содержать только строчные буквы, цифры и знак дефиса (-). Оно должно начинаться и заканчиваться буквой или цифрой и содержать от 3 до 24 символов.|
|Тип разрешения|Подстановка или создание|Выберите **Подстановка**, если вы используете отдельный процесс для создания ресурсов FHIR [Устройство](https://www.hl7.org/fhir/device.html) или [Пациент](https://www.hl7.org/fhir/patient.html) в Azure API для FHIR. Соединитель IoT будет использовать ссылки на эти ресурсы при создании ресурса FHIR [Наблюдение](https://www.hl7.org/fhir/observation.html) для представления данных устройства. Выберите **Создать**, если вы хотите, чтобы соединитель IoT создал простые ресурсы "Устройство" и "Пациент" в Azure API для FHIR, используя соответствующие значения идентификатора, имеющиеся в данных устройствах.|

После завершения установки вновь созданный соединитель IoT будет отображаться на странице **Соединители IoT**.

[![Созданный соединитель IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg#lightbox)

## <a name="configure-iot-connector-preview"></a>Настройка соединителя IoT (предварительная версия)

Соединителю IoT требуется два шаблона сопоставления, чтобы преобразовать сообщения устройства в ресурсы наблюдения FHIR: **сопоставление устройств** и **сопоставление FHIR**. Соединитель IoT не будет полностью работоспособным, пока эти сопоставления не будут отправлены.

[![Соединитель IoT с отсутствующими сопоставлениями](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg#lightbox)

Чтобы отправить шаблоны сопоставления, щелкните только что развернутый соединитель IoT, чтобы открыть страницу **соединителя IoT**.

[![Выбор соединителя IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg#lightbox)

#### <a name="device-mapping"></a>Сопоставление устройств

Шаблон сопоставления устройств преобразует данные устройства в нормализованную схему. На странице **соединителя IoT** выберите **Configure device mapping** (Настройка сопоставления устройств), чтобы открыть страницу **сопоставления устройств**. 

[![Выбор параметра настройки сопоставления устройств на странице соединителе IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg#lightbox)

На странице **сопоставление устройств** добавьте следующий скрипт в редактор JSON и нажмите кнопку **Сохранить**.

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "IotJsonPathContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.HeartRate)]",
        "patientIdExpression": "$.SystemProperties.iothub-connection-device-id",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.Body.HeartRate",
            "valueName": "hr"
          }
        ]
      }
    }
  ]
}
```

[![Сопоставление устройств соединителя IoT](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg#lightbox)


#### <a name="fhir-mapping"></a>Сопоставление FHIR

Шаблон сопоставления FHIR преобразует нормализованное сообщение в ресурс наблюдения FHIR. На странице **соединителя IoT** щелкните **Configure FHIR mapping** (Настройка сопоставления FHIR), чтобы открыть страницу **сопоставления FHIR**.  

[![Выбор параметра настройки сопоставления FHIR на странице соединителя IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg#lightbox)

На странице **FHIR mapping** (Сопоставление FHIR) добавьте следующий скрипт в редактор JSON и нажмите кнопку **Сохранить**.

```json
{
  "templateType": "CollectionFhir",
  "template": [
    {
      "templateType": "CodeValueFhir",
      "template": {
        "codes": [
          {
            "code": "8867-4",
            "system": "http://loinc.org",
            "display": "Heart rate"
          }
        ],
        "periodInterval": 0,
        "typeName": "heartrate",
        "value": {
          "unit": "count/min",
          "valueName": "hr",
          "valueType": "Quantity"
        }
      }
    }
  ]
}
```

[![Сопоставление FHIR на странице соединителе IoT](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg#lightbox)

## <a name="generate-a-connection-string"></a>Создание строки подключения

Устройству IoMT для подключения и отправки сообщений в соединитель IoT требуется строка подключения. На странице **соединителя IoT** для вновь развернутого соединителя IoT нажмите кнопку **Manage client connections** (Управление клиентскими подключениями). 

[![Выбор параметра управления клиентскими подключениями в соединителе IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg#lightbox)

На странице **Подключения** нажмите кнопку **Добавить**, чтобы создать соединение. 

[![Подключения соединителя IoT](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg#lightbox)

Укажите понятное имя для этого подключения в окне наложения и нажмите кнопку **Создать**.

[![Новое подключение соединителя IoT](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg)](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg#lightbox)

Выберите только что созданное подключение на странице **Подключения** и скопируйте значение поля **Первичная строка подключения** из окна наложения справа.

[![Строка подключения соединителя IoT](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg#lightbox)

Эта строка подключения будет использоваться на следующем шаге. 

## <a name="connect-your-devices-to-iot"></a>Подключение устройств к Интернету вещей

Azure предоставляет обширный набор продуктов Интернета вещей для подключения устройств Интернета вещей и управления ими. Вы можете создать собственное решение на основе PaaS с помощью Центра Интернета вещей Azure или начать использовать платформу управления приложениями Интернета вещей с помощью Azure IoT Central. В этом учебнике мы будем использовать Azure IoT Central, содержащий отраслевые шаблоны решений, которые помогут вам приступить к работе.

Разверните [шаблон приложения для непрерывного мониторинга состояния пациентов](https://docs.microsoft.com/azure/iot-central/healthcare/tutorial-continuous-patient-monitoring#create-an-application-template). Этот шаблон включает в себя два имитированных устройства, которые создают данные в режиме реального времени, чтобы помочь вам приступить к работе: **Smart Vitals Patch** и **Smart Knee Brace**.

> [!NOTE]
> Когда реальные устройства готовы, вы можете использовать одно приложение IoT Central для [подключения устройств](https://docs.microsoft.com/azure/iot-central/core/howto-set-up-template) и замены симуляторов устройств. Данные устройства будут автоматически поступать во FHIR. 

## <a name="connect-your-iot-data-with-the-iot-connector-preview"></a>Подключение данных Интернета вещей Azure к соединителю IoT (предварительная версия)
После развертывания приложения IoT Central два готовых виртуальных устройства начнут создавать данные телеметрии. В рамках этого учебника мы будем получать данные телеметрии из симулятора *Smart Vitals Patch* в FHIR через соединитель IoT. Чтобы экспортировать данные Интернета вещей в соединитель IoT, необходимо [настроить непрерывный экспорт данных в IoT Central](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#set-up-data-export). На странице непрерывного экспорта данных выполните следующие действия:
- Выберите *Центры событий Azure* в качестве назначения экспорта.
- Выберите значение *Использовать строку подключения* в поле **Пространство имен Центров событий**.
- Укажите строку подключения к соединителю IoT, полученную на предыдущем шаге, в поле **Строка подключения**.
- В поле **Данные для экспорта** для параметра**Телеметрия** оставьте значение *Вкл.* .

## <a name="view-device-data-in-azure-api-for-fhir"></a>Просмотр данных устройства в Azure API для FHIR

Вы можете просматривать ресурсы наблюдения FHIR, созданные соединителем IoT в Azure API для FHIR, используя Postman. Настройте [доступ к Azure API для FHIR в Postman](access-fhir-postman-tutorial.md) и выполните запрос `GET` к `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4`, чтобы просмотреть ресурсы наблюдения FHIR с информацией о частоте пульса. 

> [!TIP]
> Убедитесь, что пользователь имеет необходимые права доступа к плоскости данных Azure API для FHIR. Для назначения ролей в плоскости данных используйте [управление доступом на основе ролей Azure (Azure RBAC)](configure-azure-rbac.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если экземпляр соединителя IoT больше не нужен, его можно удалить, удалив связанную группу ресурсов или соответствующую службу Azure API для FHIR или сам экземпляр соединителя IoT. 

Чтобы напрямую удалить экземпляр соединителя IoT, выберите экземпляр на странице **соединителей IoT**, чтобы перейти на страницу **соединителя IoT**, и нажмите кнопку **Удалить**. Нажмите кнопку **Да** при запросе на подтверждение. 

[![Удаление экземпляра соединителя IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы развернули функцию соединителя IoT в ресурсе Azure API для FHIR. Чтобы узнать больше о соединителе Интернета вещей, см. следующие ресурсы.

Сведения о разных стадиях потока данных в соединителе Интернета вещей.

>[!div class="nextstepaction"]
>[Поток данных соединителя Интернета вещей ](iot-data-flow.md)

Сведения о настройке соединителя Интернета вещей с помощью шаблонов устройства и сопоставления FHIR.

>[!div class="nextstepaction"]
>[Шаблоны сопоставления для соединителя Интернета вещей](iot-mapping-templates.md)

FHIR — это зарегистрированная торговая марка организации HL7, которая используется с разрешения HL7.
