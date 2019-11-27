---
title: Краткое руководство. Поиск свободных помещений — Azure Digital Twins
description: В этом кратком руководстве вы запустите два примера приложения .NET Core для отправки смоделированных данных телеметрии движения и уровня углекислого газа в пространство Digital Twins. Цель — найти свободные помещения со свежим воздухом с помощью API управления после выполнения вычислительных процессов в облаке.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc seodec18
ms.date: 11/12/2019
ms.openlocfilehash: 44ef646328f5f55d16dfa2d6906b78866292ebd9
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123195"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>Краткое руководство. Поиск свободных помещений с помощью Azure Digital Twins

Служба Digital Twins позволяет воссоздать цифровой образ вашей физической среды. В результате вы сможете получать уведомления о событиях в своей среде и настраивать ответы на эти события.

В этом кратком руководстве используется [пара примеров .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp) для оцифровки смоделированного офисного здания, а также приведены сведения о том, как найти свободные помещения в таком здании. С помощью Digital Twins можно связать несколько датчиков со своей средой. Смоделированный датчик углекислого газа позволяет выяснить, оптимальный ли в помещении воздух. Один из примеров приложений генерирует случайные данные датчиков для визуализации этого сценария.

В следующем видео дана краткая инструкция по настройке.

>[!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Предварительные требования

1. Если у вас нет учетной записи Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

1. Два консольных приложения, запускаемые в рамках этого краткого руководства, написаны на языке C#. Установите на компьютере разработки [пакет SDK для .NET Core версии 2.1.403 или более поздней](https://www.microsoft.com/net/download). Если пакет SDK для .NET Core уже установлен, проверьте текущую версию C# на компьютере разработки. В командной строке выполните команду `dotnet --version`.

1. Скачайте [пример проекта C#](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). Извлеките архив digital-twins-samples-csharp-master.zip.

## <a name="create-a-digital-twins-instance"></a>Создание экземпляра Digital Twins

Создайте экземпляр службы Digital Twins на [портале](https://portal.azure.com), выполнив шаги, описанные в этом разделе.

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Настройка разрешений для приложения

В этом разделе пример приложения регистрируется в Azure Active Directory (Azure AD), благодаря чему у него есть доступ к экземпляру Digital Twins. Если у вас уже есть регистрация приложения Azure AD, используйте ее для своего примера. Настройте ее в соответствии с шагами, указанными в этом разделе.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="build-application"></a>Сборка приложения

Запустите сборку приложения помещения, выполнив следующие шаги.

1. Откройте окно командной строки. Перейдите к папке, в которую были извлечены файлы `digital-twins-samples-csharp-master.zip`.
1. Запустите `cd occupancy-quickstart/src`.
1. Запустите `dotnet restore`.
1. Измените файл [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json), чтобы обновить следующие переменные:
    - **ClientId:** введите идентификатор приложения из регистрации приложения Azure AD, записанный при работе с предыдущим разделом.
    - **Tenant:** введите идентификатор каталога клиента Azure AD, записанный при работе с предыдущим разделом.
    - **BaseUrl:** URL-адрес API управления экземпляра Digital Twins в формате `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Замените заполнители в этом URL-адресе значениями для своего экземпляра из предыдущего раздела.

    Сохраните обновленный файл.

## <a name="provision-graph"></a>Подготовка графа

На этом шаге подготавливается пространственный граф Digital Twins со следующими элементами:

- несколько пространств;
- одно устройство;
- два датчика;
- пользовательская функция;
- одно назначение роли.

Пространственный граф подготовлен с помощью файла [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

1. Запустите `dotnet run ProvisionSample`.

    >[!NOTE]
    >Для проверки подлинности пользователя в Azure AD используется средство Azure CLI и имя для входа устройства. Пользователь должен ввести предоставленный код для проверки подлинности на странице [входа Майкрософт](https://microsoft.com/devicelogin). После этого выполните шаги, чтобы пройти проверку подлинности. Пользователю нужно выполнить аутентификацию при запуске средства.

    >[!TIP]
    > Если при выполнении этого шага возникает ошибка `EXIT: Unexpected error: The input is not a valid Base-64 string ...`, проверьте, правильно ли скопированы переменные.

1. Шаг по подготовке может занять несколько минут. В экземпляре Digital Twins будет также подготовлен Центр Интернета вещей. Он проходит цикл, пока Центр Интернета вещей не перейдет в состояние `Running`.

    [![Подготавливается пример — Состояние=выполняется.](media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png)](media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png#lightbox)

1. В конце выполнения скопируйте строку устройства `ConnectionString`, чтобы использовать ее в примере симулятора устройства. Скопируйте только ту строку, которая выделена на этом рисунке.

    [![Копирование строки подключения](media/quickstart-view-occupancy-dotnet/digital-twins-connection-string.png)](media/quickstart-view-occupancy-dotnet/digital-twins-connection-string.png#lightbox)

    >[!TIP]
    > Вы можете просмотреть и изменить пространственный граф с помощью [средства просмотра графов Azure Digital Twins](https://github.com/Azure/azure-digital-twins-graph-viewer).

Не закрывайте окно консоли. Вы будете использовать его позже.

## <a name="send-sensor-data"></a>Отправка данных датчиков

Создайте и запустите приложение устройства симулятора датчика, выполнив приведенные ниже шаги.

1. Откройте новую командную строку. Перейдите к скачанному проекту в папке `digital-twins-samples-csharp-master`.
1. Запустите `cd device-connectivity`.
1. Запустите `dotnet restore`.
1. Измените файл [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json), чтобы обновить строку **DeviceConnectionString** строкой `ConnectionString`, приведенной выше. Сохраните обновленный файл.
1. Запустите `dotnet run`, чтобы начать отправку данных датчиков. Отправка в службу Digital Twins должна выполняться, как показано ниже.

     [![Взаимодействие устройств](media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png)](media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png#lightbox)

1. Пусть этот симулятор работает, чтобы вы могли просматривать результаты параллельно с окном, где выполняются дальнейшие действия. В этом окне появятся данные смоделированных датчиков, отправленные в службу Digital Twins. На следующем шаге в режиме реального времени будет выполнен запрос для поиска свободных помещений со свежим воздухом.

    >[!TIP]
    > Если при выполнении этого шага возникает ошибка `EXIT: Unexpected error: The input is not a valid Base-64 string ...`, проверьте, правильно ли скопирована строка `DeviceConnectionString`.

## <a name="find-available-spaces-with-fresh-air"></a>Поиск свободного пространства со свежим воздухом

Пример с датчиками имитирует значения случайных данных для двух датчиков — движения и углекислого газа. Свободные пространства со свежим воздухом определяются в примере отсутствием людей в помещении, а также уровнем углекислого газа ниже 1000 ppm. Если условие не выполняется, то пространство недоступно или качество воздуха плохое.

1. Откройте командную строку, которая использовалась на предыдущем этапе подготовки.
1. Запустите `dotnet run GetAvailableAndFreshSpaces`.
1. Разместите эту командную строку и командную строку данных датчиков рядом.

    Командная строка данных датчика отправляет смоделированные данные о движении и углекислом газе в Digital Twins каждые пять секунд. В другой командной строке в режиме реального времени считывается граф для поиска свободных помещений со свежим воздухом на основе случайных смоделированных данных. В зависимости от того, какие данные датчиков были отправлены последними, в этой строке в режиме реального времени отобразится одно из следующих условий:
   - `Room is available and air is fresh`
   - `Room is not available or air quality is poor`

     [![Получение свободного пространства со свежим воздухом](media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png)](media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png#lightbox)

Чтобы понять, что описано в этом кратком руководстве и какие API-интерфейсы были вызваны, откройте [Visual Studio Code](https://code.visualstudio.com/Download) с проектом рабочей области кода, доступным в `digital-twins-samples-csharp`. Используйте следующую команду:

```cmd
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

Код подробно рассматривается в руководствах этой серии. В них объясняется, как изменить данные конфигурации и какие API можно вызвать. Чтобы лучше ознакомиться с API-интерфейсами управления, перейдите на страницу Digital Twins Swagger:

```URL
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| ИМЯ | Заменить на |
| --- | --- |
| YOUR_INSTANCE_NAME | Имя экземпляра Digital Twins |
| YOUR_LOCATION | Регион сервера, в котором размещен ваш экземпляр |

Дополнительные сведения об [API управления службы Digital Twins](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

## <a name="clean-up-resources"></a>Очистка ресурсов

В руководствах этой серии подробно рассматриваются такие темы:

- Создание приложения для менеджеров объектов, позволяющее повысить эффективность работы сотрудников.
- Более эффективное эксплуатирование здания.

Если вы намерены перейти к изучению руководств, не удаляйте ресурсы, которые создали при работе с этим кратким руководством. Если вы не планируете продолжать работу, удалите все созданные в этом руководстве ресурсы.

1. Удалите папку, созданную при загрузке примера репозитория.
1. В меню слева на [портале Azure](https://portal.azure.com) щелкните **Все ресурсы**. Выберите свой ресурс Digital Twins. В верхней части области **Все ресурсы** выберите **Удалить**.

    > [!TIP]
    > Если ранее при удалении экземпляра Digital Twins у вас возникла проблема, запустите обновление службы, в котором эта проблема исправлена. Затем повторите попытку удалить свой экземпляр.

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве использовался простой сценарий и примеры приложений, чтобы продемонстрировать использование Digital Twins для поиска комнат с хорошими рабочими условиями. Для глубокого анализа этого сценария перейдите к этому руководству:

>[!div class="nextstepaction"]
>[Руководство по развертыванию Azure Digital Twins и настройке пространственного графа](tutorial-facilities-setup.md)
