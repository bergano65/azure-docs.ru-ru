---
title: Краткое руководство. Клиентская библиотека управления Azure для Node.js
description: В этом кратком руководстве описано, как приступить к работе с клиентской библиотекой управления Azure для Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: fcfefb2b6cd5c23e9b77d7d27413d1ff6d775bfe
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321625"
---
[Справочная документация](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices) | [Пакет (NPM)](https://www.npmjs.com/package/@azure/arm-cognitiveservices) | [Примеры](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="prerequisites"></a>Предварительные требования

* Действующая подписка Azure ([создайте бесплатную учетную запись](https://azure.microsoft.com/free/)).
* Текущая версия [Node.js](https://nodejs.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-nodejs-application"></a>создание приложения Node.js;

В окне консоли (например, cmd, PowerShell или Bash) создайте новый каталог для приложения и перейдите в него. 

```console
mkdir myapp && cd myapp
```

Выполните команду `npm init`, чтобы создать приложение узла с помощью файла `package.json`. 

```console
npm init
```

Прежде чем начать, создайте файл с именем _index.js_.

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

Установите следующие пакеты npm:

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

Файл `package.json` этого приложения будет дополнен зависимостями.

### <a name="import-libraries"></a>Импорт библиотек

Откройте скрипт _index.js_ и импортируйте следующие библиотеки.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Аутентификация клиента

Добавьте следующие поля в корень скрипта и заполните их значения с помощью созданного субъекта-службы и информации учетной записи Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

Затем добавьте следующую функцию `quickstart` для выполнения основных действий программы. Первый блок кода конструирует объект **CognitiveServicesManagementClient**, используя переменные учетных данных, указанные выше. Этот объект необходим для всех операций управления Azure.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>Вызов функций управления

Добавьте следующий код в конец функции `quickstart`, чтобы получить список доступных ресурсов, создать пример ресурса, вывести список собственных ресурсов, а затем удалить пример ресурса. Эти функции будут определены позже.

## <a name="create-a-cognitive-services-resource"></a>Создание ресурса Cognitive Services

### <a name="choose-a-service-and-pricing-tier"></a>Выбор службы и ценовой категории

При создании ресурса необходимо знать, какой вид службы вы хотите использовать, а также выбрать [ценовую категорию](https://azure.microsoft.com/pricing/details/cognitive-services/) (или номер SKU). Вы будете использовать эту и другую информацию в качестве параметров при создании ресурса. Следующая функция выводит список доступных "видов" служб Cognitive Services.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="create-a-cognitive-services-resource"></a>Создание ресурса Cognitive Services

Чтобы создать ресурс Cognitive Services и подписываться на него, используйте функцию **Create**. Эта функция добавляет новый оплачиваемый ресурс в передаваемую группу ресурсов. При создании ресурса необходимо знать, какой вид службы вы хотите использовать, а также выбрать ценовую категорию (или номер SKU) и расположение Azure. Следующая функция использует все эти аргументы и создает ресурс.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

## <a name="view-your-resources"></a>Просмотр ресурсов

Чтобы просмотреть все ресурсы в учетной записи Azure (для всех групп ресурсов), используйте следующую функцию:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>Удаление ресурса

Следующая функция удаляет указанные ресурсы из заданной группы ресурсов.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

## <a name="run-the-application"></a>Выполнение приложения

Добавьте следующий код в нижнюю часть скрипта, чтобы вызывать основную функцию `quickstart` при обработке ошибок.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

Затем в окне консоли запустите приложение с помощью команды `node`.

```console
node index.js
```

## <a name="see-also"></a>См. также

* [Справочная документация по пакету SDK для управления Azure](https://docs.microsoft.com/javascript/api/@azure/arm-cognitiveservices/?view=azure-node-latest)
* [Общие сведения об Azure Cognitive Services](../../Welcome.md)
* [Проверка подлинности запросов к Azure Cognitive Services](../../authentication.md)
* [Создание ресурса с помощью портала Azure](../../cognitive-services-apis-create-account.md)