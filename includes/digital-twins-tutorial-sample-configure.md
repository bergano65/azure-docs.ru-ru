---
author: baanders
description: Файл include для руководств Azure Digital Twins. Настройка примера проекта
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 3b68df1b3fc2f03d7659205fe03fdae09ecc3f7a
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87827373"
---
## <a name="configure-the-sample-project"></a>Настройка примера проекта

Затем настройте пример клиентского приложения, который будет взаимодействовать с вашим экземпляром Azure Digital Twins. Если у вас еще нет примера проекта, скачайте его с целевой страницы с [*примерами Azure Digital Twins*](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples), нажав кнопку *Download ZIP* (Скачать ZIP-файл) под заголовком.

Перейдите к скачанному файлу на компьютере и распакуйте его.

В распакованной папке перейдите во вложенную папку _AdtSampleApp_. Откройте _**AdtE2ESample.sln**_ в Visual Studio 2019. 

В Visual Studio используйте панель *Обозреватель решений*, чтобы создать копию файла _SampleClientApp > **serviceConfig.json.TEMPLATE**_ (для копирования и вставки можно использовать меню, выбираемые правой кнопкой мыши). Переименуйте копию *serviceConfig.json*. Это будет предварительно заданный файл JSON с необходимыми переменными конфигурации для запуска проекта.

Выберите файл *serviceConfig.json*, чтобы открыть его в окне редактирования. Измените `tenantId` на *идентификатор каталога*, `clientId` на *идентификатор приложения* и `instanceUrl` на URL-адрес *hostName* экземпляра Azure Digital Twins (с *https://* в начале, как показано ниже).

```json
{
  "tenantId": "<your-directory-ID>",
  "clientId": "<your-application-ID>",
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```



Сохраните файл и закройте его. 

Затем настройте файл *serviceConfig.json*, который будет скопирован в выходной каталог при построении *SampleClientApp*. Для этого щелкните правой кнопкой мыши файл *serviceConfig.json* и выберите пункт *Свойства*. В инспекторе *Свойства* измените значение свойства *Копировать во внешний каталог* на *Копировать, если новее*.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Выдержка из окна Visual Studio, в котором отображается область Обозревателя решений с выделенным serviceConfig.json, а также панель свойств со свойством Копировать во внешний каталог, для которого задано значение Копировать, если новее" border="false":::

Не закрывайте проект _**AdtE2ESample**_ в Visual Studio, чтобы продолжить его использование в этом руководстве.

