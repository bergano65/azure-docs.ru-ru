---
title: Тестирование определения пользовательского интерфейса для Управляемых приложений Azure | Документация Майкрософт
description: В этой статье приводятся сведения о тестировании пользовательского интерфейса для создания Управляемого приложения Azure с помощью портала.
author: tfitzmac
ms.service: managed-applications
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 54eb2df06df56c33e1a3cd74e7a4a93c07aab682
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575664"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Тестирование интерфейса портала для управляемых приложений Azure

После [создания файла createUiDefinition. JSON](create-uidefinition-overview.md) для управляемого приложения необходимо протестировать взаимодействие с пользователем. Чтобы упростить тестирование, используйте изолированную среду, которая загружает файл на портал. Вам не нужно развертывать управляемое приложение. Песочница представляет пользовательский интерфейс в текущем полноэкранном портале. Также можно использовать скрипт для тестирования интерфейса. В этой статье описаны оба подхода. Песочница является рекомендуемым способом предварительного просмотра интерфейса.

## <a name="prerequisites"></a>Предварительные требования

* Файл **createUiDefinition.json**. Если у вас нет этого файла, скопируйте [Пример файла](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="use-sandbox"></a>Использование песочницы

1. Откройте [песочницу создание определения пользовательского интерфейса](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Показывать песочницу](./media/test-createuidefinition/show-sandbox.png)

1. Замените пустое определение содержимым файла createUiDefinition. JSON. Выберите **Предварительный просмотр**.

   ![Выберите Предварительный просмотр](./media/test-createuidefinition/select-preview.png)

1. Откроется созданная форма. Вы можете пошагово пройти взаимодействие с пользователем и заполнить значения.

   ![Отобразить форму](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Устранение неполадок

Если форма не отображается после выбора **предварительной версии**, может возникнуть синтаксическая ошибка. Найдите красный индикатор на правой полосе прокрутки и перейдите к нему.

![Показывать синтаксическую ошибку](./media/test-createuidefinition/show-syntax-error.png)

Если форма не отображается, а отображается значок облака с отрывным удалением, то форма содержит ошибку, например, отсутствующее свойство. Откройте веб-Средства для разработчиков в браузере. В **консоли** отображаются важные сообщения об интерфейсе.

![Показать ошибку](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Использование скрипта теста

Чтобы протестировать свой интерфейс на портале, скопируйте один из следующих сценариев на локальный компьютер:

* [Скрипт для загрузки на стороне PowerShell — AZ Module](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [Сценарий загрузки на стороне PowerShell для модуля Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI side-load script](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Чтобы просмотреть файл интерфейса на портале, запустите загруженный сценарий. Сценарий создаст учетную запись хранения в вашей подписке Azure и загрузит файл createUiDefinition.json в эту учетную запись хранения. Учетная запись хранения создается при первом запуске скрипта или в случае удаления учетной записи хранения. Если учетная запись хранения уже существует в подписке Azure, скрипт повторно использует ее. Затем он откроет портал и загрузит файл из учетной записи хранения.

Укажите расположение учетной записи хранения и папку, в которой содержится файл createUiDefinition.json.

Для PowerShell используйте команду:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Для интерфейса командной строки Azure:

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Если файл createUiDefinition.json находится в той же папке, что и скрипт, и вы уже создали учетную запись хранения, указывать эти параметры не нужно.

Для PowerShell используйте команду:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Для интерфейса командной строки Azure:

```azurecli
./sideload-createuidef.sh
```

Сценарий откроет новую вкладку в браузере. В ней отобразится портал с интерфейсом для создания управляемого приложения.

Укажите значения для полей. По завершении вы увидите значения, которые передаются в шаблон, который можно найти в консоли средств разработчика в браузере.

![Показать значения](./media/test-createuidefinition/show-json.png)

Эти значения можно использовать как файл параметров для тестирования шаблона развертывания.

Если портал зависает на экране сводки, в выходном разделе может появиться ошибка. Например, возможно, вы ссылаетесь на несуществующий элемент управления. Если параметр в выходных данных пуст, параметр может ссылаться на несуществующее свойство. Например, ссылка на элемент управления является допустимой, а ссылка на свойство — нет.

## <a name="test-your-solution-files"></a>Тестирование файлов решения

Теперь, когда вы проверили, что ваш интерфейс портала работает так, как ожидается, пришло время проверить, что ваш файл createUiDefinition правильно интегрирован с файлом mainTemplate.json. Можно запустить тестирование сценария проверки для проверки содержимого файлов решения, включая файл createUiDefinition. Сценарий проверяет синтаксис JSON, регулярные выражения в текстовых полях и обеспечивает соответствие выходных значений интерфейса портала параметрам шаблона. Со сведениями о запуске этого сценария можно ознакомиться в статье, посвященной [запуску статических проверок шаблонов](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests).

## <a name="next-steps"></a>Следующие шаги

После проверки интерфейса портала узнайте, как [обеспечить доступность управляемых приложений Azure в Marketplace](publish-marketplace-app.md).
