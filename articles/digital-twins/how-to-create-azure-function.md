---
title: Настройка функции в Azure для обработки данных
titleSuffix: Azure Digital Twins
description: Узнайте, как создать функцию в Azure, которая может получать доступ и вызываться цифровым двойников.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6f74f973abc33d809624bd8abd5a514a52ccfe70
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602706"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Подключение приложений функций в Azure для обработки данных

Обновление цифровых двойников на основе данных осуществляется с помощью [**маршрутов событий**](concepts-route-events.md) через ресурсы вычислений, таких как функция, созданная с помощью [функций Azure](../azure-functions/functions-overview.md). Функции можно использовать для обновления цифрового двойника в ответ на следующее:
* данные телеметрии устройства, поступающие из центра Интернета вещей
* изменение свойства или другие данные, поступающие из другого цифрового двойника в графе двойника

В этой статье описывается создание функции в Azure для использования с цифровым двойников Azure. 

Ниже приведен обзор шагов, которые он содержит.

1. Создание проекта функций Azure в Visual Studio
2. Написание функции с помощью триггера [сетки событий](../event-grid/overview.md)
3. Добавление кода проверки подлинности в функцию (для доступа к Azure Digital двойников)
4. Публикация приложения-функции в Azure
5. Настройка [безопасного](concepts-security.md) доступа для приложения функции

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>Предварительные требования: Настройка экземпляра Digital двойников для Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Создание приложения-функции в Visual Studio

В Visual Studio 2019 выберите _файл > создать > проект_ и найдите шаблон _функции Azure_ , нажмите кнопку _Далее_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio: диалоговое окно создания проекта":::

Укажите имя для приложения-функции и нажмите кнопку _создать_.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio: Настройка нового проекта":::

Выберите тип *триггера сетки событий* приложения функции и нажмите кнопку _создать_.

:::image type="content" source="media/how-to-create-azure-function/eventgridtrigger-function.png" alt-text="Visual Studio: диалоговое окно триггера проекта функций Azure":::

После создания приложения-функции Visual Studio будет иметь автоматически заполняемый пример кода в файле **Function.CS** в папке проекта. Эта небольшая функция используется для записи событий в журнал.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio: окно проекта с образцом кода":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Написать функцию с помощью триггера сетки событий

Вы можете написать функцию, добавив пакет SDK в приложение-функцию. Приложение-функция взаимодействует с Azure Digital двойников с помощью [пакета SDK Azure Digital двойников для .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 

Чтобы использовать пакет SDK, необходимо включить в проект следующие пакеты. Можно либо установить пакеты с помощью диспетчера пакетов NuGet Visual Studio, либо добавить пакеты с помощью `dotnet` программы командной строки. Выберите один из следующих методов. 

**Вариант 1. Добавление пакетов с помощью диспетчера пакетов Visual Studio:**
    
Это можно сделать, щелкнув проект правой кнопкой мыши и выбрав пункт _Управление пакетами NuGet_ в списке. Затем в открывшемся окне выберите вкладку _Обзор_ и найдите следующие пакеты. Выберите _установить_ и _принять_ лицензионное соглашение для установки пакетов.

* `Azure.DigitalTwins.Core`
* `Azure.Identity`
* `System.Net.Http`
* `Azure.Core`

**Вариант 2. Добавление пакетов с помощью `dotnet` программы командной строки:**

Кроме того, можно использовать следующие `dotnet add` команды в программе командной строки:

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
dotnet add package System.Net.Http
dotnet add package Azure.Core
```

Затем в Visual Studio обозреватель решений откройте файл _Function.CS_ , где имеется образец кода, и добавьте в функцию следующие операторы _using_ . 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Добавление кода проверки подлинности в функцию

Теперь вы можете объявить переменные уровня класса и добавить код проверки подлинности, который позволит функции получить доступ к Azure Digital двойников. Вы добавите следующий код в функцию в файле {имя функции}. cs.

* Чтение URL-адреса службы ADT в качестве переменной среды. Рекомендуется считать URL-адрес службы из переменной среды, а не жестко кодировать его в функции.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* Статическая переменная для хранения экземпляра HttpClient. HttpClient довольно дорого создавать, и мы хотим избежать этого при каждом вызове функции.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* Учетные данные управляемого удостоверения можно использовать в функциях Azure.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* Добавьте локальную переменную _дигиталтвинсклиент_ внутри функции, чтобы разместить свой экземпляр клиента Azure Digital двойников в проекте функции. *Не* делайте эту переменную статической внутри класса.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* Добавьте проверку значения NULL для _адтинстанцеурл_ и заключите логику функции в блок try catch, чтобы перехватить все исключения.

После этих изменений код функции будет выглядеть следующим образом:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

## <a name="publish-the-function-app-to-azure"></a>Публикация приложения-функции в Azure

Чтобы опубликовать проект в приложении-функции в Azure, выберите проект функции (не решение) в обозреватель решений и нажмите кнопку **опубликовать**.

> [!IMPORTANT] 
> Публикация в приложении-функции в Azure влечет за собой дополнительную плату за подписку, не зависящую от Azure Digital двойников.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function.png" alt-text="Visual Studio: публикация функции в Azure":::

Выберите **Azure** в качестве целевого объекта публикации и нажмите кнопку **Далее**.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-1.png" alt-text="Visual Studio: диалоговое окно публикации функций Azure, выбор Azure ":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-2.png" alt-text="Visual Studio: диалоговое окно функции публикации выберите Azure приложение-функция (Windows) или (Linux) на основе компьютера":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-3.png" alt-text="Visual Studio: диалоговое окно публикации функции, создание новой функции Azure":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-4.png" alt-text="Visual Studio: диалоговое окно функции публикации, заполните поля и выберите Создать.":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-5.png" alt-text="Visual Studio: диалоговое окно функции публикации, выберите в списке приложение функции и завершите его работу.":::

На следующей странице введите нужное имя для нового приложения-функции, группы ресурсов и других сведений.
Чтобы ваше приложение-функция могло получить доступ к Azure Digital двойников, оно должно иметь управляемое системой удостоверение и иметь разрешения на доступ к вашему экземпляру Azure Digital двойников.

Затем можно настроить безопасный доступ для функции с помощью интерфейса командной строки или портал Azure. Выберите один из следующих методов.

## <a name="set-up-security-access-for-the-function-app"></a>Настройка безопасного доступа для приложения функции
Вы можете настроить доступ к безопасности для приложения функции, используя один из следующих вариантов:

### <a name="option-1-set-up-security-access-for-the-function-app-using-cli"></a>Вариант 1. Настройка безопасного доступа для приложения функции с помощью интерфейса командной строки

В схеме функций из предыдущих примеров требуется передать в него токен носителя, чтобы иметь возможность проходить проверку подлинности в Azure Digital двойников. Чтобы убедиться, что этот токен носителя передан, необходимо настроить [управляемое удостоверение службы (MSI)](../active-directory/managed-identities-azure-resources/overview.md) для приложения-функции. Это необходимо сделать только один раз для каждого приложения функции.

Вы можете создать управляемое системой удостоверение и назначить удостоверение приложения-функции для роли _**владельца данных Azure Digital двойников**_ для своего экземпляра Azure Digital двойников. Это предоставит приложению функции в экземпляре разрешение на выполнение действий плоскости данных. Затем сделайте URL-адрес экземпляра Azure Digital двойников доступным для вашей функции, задав переменную среды.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

Используйте [Azure Cloud Shell](https://shell.azure.com) для выполнения команд.

С помощью следующей команды создайте управляемое системой удостоверение. Запишите значение поля _principalId_ в выходных данных команды.

```azurecli-interactive 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Используйте значение _principalId_ в следующей команде, чтобы назначить удостоверение приложения-функции роли _владельца Azure Digital Twins_ для вашего экземпляра Azure Digital Twins.

```azurecli-interactive 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```
Наконец, можно сделать так, чтобы URL-адрес вашего экземпляра Azure Digital двойников был доступен для вашей функции, задав переменную среды. Дополнительные сведения о настройке переменных среды см. в разделе [*переменные среды*](/sandbox/functions-recipes/environment-variables). 

> [!TIP]
> URL-адрес экземпляра Azure Digital двойников создается путем добавления *https://* в начало *имени узла* для цифрового двойникова Azure. Чтобы просмотреть имя узла, а также все свойства экземпляра, можно запустить `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-function-app-using-azure-portal"></a>Вариант 2. Настройка безопасного доступа для приложения функции с помощью портал Azure

Управляемое удостоверение, назначенное системой, позволяет ресурсам Azure проходить проверку подлинности в облачных службах (например, Azure Key Vault) без сохранения учетных данных в коде. После включения все необходимые разрешения можно предоставить через Управление доступом на основе ролей в Azure. Жизненный цикл этого типа управляемого удостоверения связан с жизненным циклом этого ресурса. Кроме того, каждый ресурс (например, виртуальная машина) может иметь только одно назначенное системой управляемое удостоверение.

В [портал Azure](https://portal.azure.com/)найдите _приложение функции_ в строке поиска с именем приложения функции, созданным ранее. Выберите *приложение-функция* из списка. 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-functionapp.png" alt-text="Портал Azure: Поиск приложения функции":::

В окне приложение функции выберите _удостоверение_ на панели навигации слева, чтобы включить управляемое удостоверение.
На вкладке _назначенная система_ переключите _состояние_ на вкл. и _Сохраните_ его. Вы увидите всплывающее окно для _включения управляемого удостоверения, назначенного системой_.
Нажмите кнопку _Да_ . 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Портал Azure: Включить управляемое системой удостоверение":::

Вы можете проверить в уведомлениях о том, что функция успешно зарегистрирована в Azure Active Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Портал Azure: уведомления":::

Также обратите внимание на **идентификатор объекта** , показанный на странице _удостоверений_ , как он будет использоваться в следующем разделе.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Скопируйте идентификатор объекта для использования в будущем":::

### <a name="assign-access-roles-using-azure-portal"></a>Назначение ролей доступа с помощью портал Azure

Нажмите кнопку _назначения ролей Azure_ , после чего откроется страница *назначения ролей Azure* . Затем выберите _+ добавить назначение ролей (Предварительная версия)_.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Портал Azure: Добавление назначения ролей":::

На открывшейся странице _Добавление назначения ролей (Предварительная версия)_ выберите:

* _Область_ — группа ресурсов.
* _Подписка_: выберите подписку Azure.
* _Группа ресурсов_. Выберите группу ресурсов из раскрывающегося списка.
* _Роль_: выберите _Azure Digital двойников Data Owner_ в раскрывающемся списке.

Затем сохраните сведения, нажав кнопку Save ( _сохранить_ ).

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Портал Azure: Добавление назначения ролей (Предварительная версия) ":::

### <a name="configure-application-settings-using-azure-portal"></a>Настройка параметров приложения с помощью портал Azure

Чтобы сделать URL-адрес своего экземпляра Digital двойников доступным для вашей функции, можно задать переменную среды. Дополнительные сведения об этом см. в разделе [*переменные среды*](/sandbox/functions-recipes/environment-variables). Параметры приложения предоставляются как переменные среды для доступа к экземпляру Digital двойников. 

Для создания параметра приложения потребуются ADT_INSTANCE_URL.

ADT_INSTANCE_URL можно получить, добавив **_https://_** в имя узла экземпляра. В портал Azure можно найти имя узла Digital двойников, выполнив поиск своего экземпляра на панели поиска. Затем на левой панели навигации выберите _Обзор_ , чтобы просмотреть _имя узла_. Скопируйте это значение, чтобы создать параметр приложения.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Портал Azure: Overview — > Копировать имя узла для использования в поле _Value_.":::

Теперь можно создать параметр приложения, выполнив следующие действия:

* Найдите приложение, используя имя приложения функции в строке поиска, и выберите приложение функции из списка.
* Выберите пункт _Конфигурация_ на панели навигации слева, чтобы создать новый параметр приложения.
* На вкладке _Параметры приложения_ выберите _+ создать параметр приложения_ .

:::image type="content" source="media/how-to-create-azure-function/search-for-azure-function.png" alt-text="Портал Azure: Поиск существующего приложения функции":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Портал Azure: Настройка параметров приложения":::

В открывшемся окне используйте значение, скопированное из выше, чтобы создать параметр приложения. \
_Имя_  : ADT_SERVICE_URL \
_Значение_ : https://{ваш-Azure-Digital-двойников-hostname}

Нажмите кнопку _ОК_ , чтобы создать параметр приложения.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Портал Azure: Добавление параметров приложения.":::

Параметры приложения можно просмотреть с помощью имени приложения в поле _имя_ . Затем сохраните параметры приложения, нажав кнопку _сохранить_ .

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Портал Azure: Просмотр созданного приложения и перезапуск приложения":::

Все изменения параметров приложения требуют перезапуска приложения. Нажмите кнопку _продолжить_ , чтобы перезапустить приложение.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Портал Azure: сохранение параметров приложения":::

Вы можете просмотреть параметры приложения, щелкнув значок _уведомления_ . Если параметр приложения не создан, можно повторно добавить параметр приложения, выполнив описанный выше процесс.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Портал Azure: уведомления об обновлении параметров приложения":::

## <a name="next-steps"></a>Следующие шаги

В этой статье описано, как настроить приложение-функцию в Azure для использования с цифровым двойников Azure. Затем можно подписать функцию на службу "Сетка событий", чтобы прослушать конечную точку. Эта конечная точка может быть следующей:
* Конечная точка сетки событий, присоединенная к Azure Digital двойников для обработки сообщений, поступающих от самого себя в Azure Digital двойников (например, сообщений об изменении свойств, сообщений телеметрии, созданных [цифровым двойников](concepts-twins-graph.md) в графе двойника, или сообщений о жизненном цикле).
* Разделы системы IoT, используемые центром Интернета вещей для отправки данных телеметрии и других событий устройства
* Конечная точка сетки событий, получающая сообщения от других служб

Далее Узнайте, как создать базовую функцию для приема данных центра Интернета вещей в Azure Digital двойников:
* [*Пошаговое руководство. прием данных телеметрии из центра Интернета вещей*](how-to-ingest-iot-hub-data.md)
