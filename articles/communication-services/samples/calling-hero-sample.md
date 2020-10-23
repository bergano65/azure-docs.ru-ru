---
title: Пример функции группового вызова
titleSuffix: An Azure Communication Services sample overview
description: Сведения о принципе работы примера функции группового вызова с использованием Служб коммуникации Azure для разработчиков.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: cfb6a7ba10c388cadb19268ee1431fe523a0cfa4
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126332"
---
# <a name="get-started-with-the-group-calling-hero-sample"></a>Начало работы с примером функции группового вызова

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> Add links to our Hero Sample repo when the sample is publicly available.
---->

Пример **Group Calling Hero Sample** Служб коммуникации Azure демонстрирует, как использовать клиентскую библиотеку веб-вызовов Служб коммуникации для создания группового вызова.

Из краткого руководства мы узнаем, как работает пример и запустим его на локальном компьютере. Затем мы развернем пример в Azure с помощью собственных ресурсов Служб коммуникации Azure.

> [!IMPORTANT]
> [Скачайте пример с GitHub.](https://github.com/Azure-Samples/communication-services-web-calling-hero)

## <a name="overview"></a>Обзор

Пример содержит клиентское и серверное приложения. **Клиентское приложение** — это веб-приложение React/Redux, использующее платформу пользовательского интерфейса Fluent от Майкрософт. Это приложение отправляет запросы в **серверное приложение** ASP.NET Core, которое помогает клиентскому приложению подключаться к Azure. 

Вот как выглядит этот пример:

:::image type="content" source="./media/calling/landing-page.png" alt-text="Снимок экрана: целевая страница примера приложения.&quot;:::

При нажатии кнопки &quot;Start a call" (Начать вызов) веб-приложение получает маркер доступа пользователя из серверного приложения. Затем этот маркер используется для подключения клиентского приложения к Службам коммуникации Azure. После получения маркера вам будет предложено указать камеру и микрофон, которые вы хотите использовать. Вы сможете отключать и включать устройства с помощью переключателей.

:::image type="content" source="./media/calling/pre-call.png" alt-text="Снимок экрана: целевая страница примера приложения.&quot;:::

При нажатии кнопки &quot;Start a call":::

Настройте отображаемое имя и устройства, а затем присоединитесь к сеансу вызова. Теперь вы увидите основной холст с основными возможностями вызова.

:::image type="content" source="./media/calling/main-app.png" alt-text="Снимок экрана: целевая страница примера приложения.&quot;:::

При нажатии кнопки &quot;Start a call" в правом верхнем углу. На боковой панели с участниками отображается список участников и ссылка для приглашения в чат дополнительных пользователей. Боковая панель с параметрами позволяет настроить параметры микрофона и камеры.

Ниже вы найдете дополнительные сведения о предварительных требованиях и шагах по настройке примера.

## <a name="prerequisites"></a>Предварительные требования

- Создайте учетную запись Azure с активной подпиской. Дополнительные сведения см. в разделе [Создание учетной записи бесплатно](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js (12.18.4 и более поздние версии).](https://nodejs.org/en/download/)
- [Visual Studio (2019 и более поздние версии).](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (установите 32- или 64-разрядную версию в соответствии с версией экземпляра Visual Studio).
- Создайте ресурс Служб коммуникации Azure. Дополнительные сведения см. в статье [Краткое руководство по созданию ресурсов Служб коммуникации и управлению ими](../quickstarts/create-communication-resource.md). Для этого краткого руководства необходимо записать **строку подключения** к ресурсу.

## <a name="locally-deploy-the-service--client-applications"></a>Локальное развертывание клиентского приложения и приложения службы

Пример группового вызова по сути состоит из двух приложений: ClientApp и Service.NET.

Если мы хотим создать развертывание в локальной среде, нам нужно запустить оба приложения. При входе в серверное приложение с помощью браузера для взаимодействия с пользователем будет использоваться локально развернутое клиентское приложение ClientApp.

Вы можете проверить пример локально, открыв несколько сеансов браузера с URL-адресом вызова, чтобы имитировать вызов с несколькими пользователями.

## <a name="before-running-the-sample-for-the-first-time"></a>Действия перед первым запуском примера

1. Откройте экземпляр PowerShell, Терминал Windows, командную строку или аналогичную среду и перейдите к каталогу, в который вы хотите клонировать пример.
2. `git clone https://github.com/Azure/Communication.git`
3. Получите `Connection String` с помощью портала Azure. Дополнительные сведения о строках подключения см. в статье [Создание ресурсов Служб коммуникации Azure и управление ими](../quickstarts/create-communication-resource.md).
4. Получив строку подключения (`Connection String`), добавьте ее в файл **Calling/appsetting.json**, который расположен в папке .NET службы. Сохраните строку подключения в переменную: `ResourceConnectionString`.

### <a name="local-run"></a>Запуск в локальной среде

1. Перейдите в папку Calling и откройте решение `Calling.csproj` в Visual Studio.
2. Запустите проект `Calling`. Браузер откроет страницу по адресу localhost:5001.

#### <a name="troubleshooting"></a>Диагностика

- Решение не компилируется, а выдает ошибки во время установки или сборки NPM.

   Попробуйте очистить и перестроить проекты.

## <a name="publish-the-sample-to-azure"></a>Публикация примера в Azure

1. Щелкните правой кнопкой мыши проект `Calling` и выберите команду "Опубликовать".
2. Создайте профиль публикации и выберите подписку Azure.
3. Перед публикацией добавьте строку подключения с `Edit App Service Settings`, а затем укажите `ResourceConnectionString` в качестве ключа и строку подключения (скопированную из appsettings.json) в качестве значения.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку на Службы коммуникации, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней. См. сведения об [очистке ресурсов](../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Дальнейшие действия

>[!div class="nextstepaction"] 
>[Скачайте пример с GitHub.](https://github.com/Azure-Samples/communication-services-web-calling-hero)

Дополнительные сведения см. в следующих статьях:

- Изучите, как правильно [использовать клиентскую библиотеку вызовов](../quickstarts/voice-video-calling/calling-client-samples.md).
- Изучите [возможности клиентской библиотеки для вызовов](../quickstarts/voice-video-calling/calling-client-samples.md).
- Узнайте больше о [принципе работы функции вызовов](../concepts/voice-video-calling/about-call-types.md).

## <a name="additional-reading"></a>Дополнительные материалы

- [Службы коммуникации Azure (GitHub)](https://github.com/Azure/communication) — дополнительные примеры и сведения на официальной странице GitHub
- [Redux](https://redux.js.org/) — управление состоянием на стороне клиента
- [FluentUI](https://aka.ms/fluent-ui) — библиотека пользовательского интерфейса, поддерживаемая корпорацией Майкрософт
- [React](https://reactjs.org/) — библиотека для создания пользовательских интерфейсов
- [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true) — платформа для создания веб-приложений
