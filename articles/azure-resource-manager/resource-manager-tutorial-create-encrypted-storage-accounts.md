---
title: Создание шаблона Azure Resource Manager для развертывания зашифрованной учетной записи хранения | Документация Майкрософт
description: Используйте Visual Studio Code, чтобы создать шаблон для развертывания зашифрованной учетной записи хранения.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/18/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a3fc3e0cc30b379c84ac0ba12f733d2db4e41587
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945796"
---
# <a name="tutorial-create-an-azure-resource-manager-template-for-deploying-an-encrypted-storage-account"></a>Руководство. Создание шаблона Azure Resource Manager для развертывания зашифрованной учетной записи хранения

Узнайте, как найти сведения для заполнения шаблона Azure Resource Manager.

В этом руководстве для создания учетной записи хранения Azure используется базовый шаблон из шаблонов быстрого запуска Azure.  С помощью шаблонов справочной документации можно настроить базовый шаблон, чтобы создать зашифрованную учетную запись хранения.

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Открытие шаблона быстрого запуска
> * Общие сведения о шаблоне
> * Изменение шаблона
> * Развертывание шаблона

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* [Visual Studio Code](https://code.visualstudio.com/) с [расширением Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Открытие шаблона быстрого запуска

Шаблон, используемый в этом кратком руководстве, называется [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Создание стандартной учетной записи хранения). Шаблон определяет ресурс учетной записи службы хранилища Azure.

1. В Visual Studio Code выберите **Файл**>**Открыть файл**.
2. Скопируйте приведенный ниже URL-адрес и вставьте его в поле **Имя файла**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Чтобы открыть файл, выберите **Открыть**.
4. Выберите **Файл**>**Сохранить как**, чтобы сохранить файл в качестве **azuredeploy.json** на локальном компьютере.

## <a name="understand-the-schema"></a>Изучение схемы

Из VS Code сверните шаблон до корневого уровня. Имеется простейшая структура со следующими элементами.

![Простейшая структура шаблона Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

* Параметр **$schema** указывает расположение файла схемы JSON, который описывает версию языка шаблона.
* Параметр **contentVersion** задает этому элементу любое значение, чтобы документировать важные изменения в шаблоне.
* Параметр **parameters** указывает значения, которые предоставляются при выполнении развертывания для настройки развертывания ресурсов.
* Параметр **variables** указывает значения, используемые в виде фрагментов JSON в шаблоне для упрощения выражений на языке шаблона.
* Параметр **resources** указывает типы ресурсов, которые развертываются или обновляются в группе ресурсов.
* Параметр **outputs** указывает значения, возвращаемые после развертывания.

## <a name="use-parameters"></a>Использование параметров

Параметры позволяют настраивать развертывание путем предоставления значений, предназначенных для конкретной среды. Параметры, определенные в шаблоне, используются при задании значений для учетной записи хранения.

![Параметры шаблона Azure Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-parameters.png)

В этом шаблоне определены два параметра. Обратите внимание, что в location.defaultValue используется функция шаблона.

```json
"defaultValue": "[resourceGroup().location]",
```

Функция resourceGroup() возвращает объект, представляющий текущую группу ресурсов. Полный список функций шаблонов см. в статье [Функции шаблонов диспетчера ресурсов Azure](./resource-group-template-functions.md).

Использование параметров, определенных в шаблоне.

```json
"location": "[parameters('location')]",
"name": "[parameters('storageAccountType')]"
```

## <a name="use-variables"></a>Использование переменных

Переменные позволяют создавать значения, которые могут использоваться в разных частях шаблона. Переменные помогают уменьшить сложность шаблонов.

![Переменные шаблона Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-variables.png)

Этот шаблон определяет одну переменную *storageAccountName*. В определении используются две функции шаблона.

- Функция **concat()** объединяет строки. Дополнительные сведения в разделе [concat](./resource-group-template-functions-string.md#concat).
- Функция **uniqueString()** создает детерминированную хэш-строку на основании значений, указанных как параметры. Каждая учетная запись хранения должна иметь уникальное имя в Azure. Эта функция предоставляет уникальную строку. Больше строковых функций в статье [Строковые функции для шаблонов Azure Resource Manager](./resource-group-template-functions-string.md).

Использование переменной, заданной в шаблоне.

```json
"name": "[variables('storageAccountName')]"
```

## <a name="edit-the-template"></a>Изменение шаблона

В этом руководстве описано, как определить шаблон для создания зашифрованной учетной записи хранения.  Этот шаблон создает только базовую незашифрованную учетную запись хранения. Связанную с шифрованием конфигурацию см. в справочнике по шаблонам учетной записи хранения Azure.

1. Перейдите к [шаблонам Azure](https://docs.microsoft.com/azure/templates/).
2. В **фильтр по названию** введите **учетные записи хранения**.
3. Как показано на следующем снимке экрана, выберите **ссылку или ссылка шаблона, или хранилище, или учетные записи хранения**.

    ![Учетная запись хранения ссылки на шаблон Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

4. Найдите информацию о шифровании в определениях.  

    ```json
    "encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true
            }
        }
    }
    ```
5. В Visual Studio Code измените шаблон, чтобы окончательный элемент ресурсов выглядит как:
    
    ![Зашифрованные ресурсы учетной записи хранения шаблона Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>Развертывание шаблона

Дополнительные сведения о [развертывании шаблона](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) см. в кратком руководстве по Visual Studio Code.

На следующем рисунке показаны команды CLI для вывода созданной учетной записи хранения. Это означает, что для хранилища BLOB-объектов включено шифрование.

![Зашифрованная учетная запись хранения Azure Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.  В группе ресурсов должно появится шесть ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве описано, как использовать справочник по шаблонам для настройки существующего шаблона. Узнайте, как создать несколько экземпляров учетной записи хранения

> [!div class="nextstepaction"]
> [Развертывание нескольких экземпляров ресурсов в шаблонах Azure Resource Manager](./resource-manager-tutorial-create-multiple-instances.md)
