---
title: Использование справочной документации по шаблонам
description: Создание шаблона для развертывания зашифрованной учетной записи хранения с помощью справочника по шаблонам Azure Resource Manager.
author: mumian
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 99ec64529b90c7a80aea62090f80c55cf4e23510
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326482"
---
# <a name="tutorial-utilize-the-azure-resource-manager-template-reference"></a>Руководство. Использование шаблонов Azure Resource Manager

Узнайте, как найти информацию о схеме шаблона и использовать ее для создания шаблонов Azure Resource Manager.

В этом руководстве вы используете базовый шаблон из шаблонов быстрого запуска Azure. С помощью шаблонов справочной документации можно настроить шаблон, чтобы создать зашифрованную учетную запись хранения.

![Справочник по шаблонам Resource Manager: развернуть зашифрованную учетную запись хранения](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-tutorial-deploy-encrypted-storage-account.png)

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Открытие шаблона быстрого запуска
> * Общие сведения о шаблоне
> * Поиск ссылки на шаблон
> * Изменение шаблона
> * Развертывание шаблона

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* Visual Studio Code с расширением средств Resource Manager. Дополнительные сведения см. в статье [Use Visual Studio Code to create Azure Resource Manager templates](./resource-manager-tools-vs-code.md) (Создание шаблонов Azure Resource Manager с помощью Visual Studio Code).

## <a name="open-a-quickstart-template"></a>Открытие шаблона быстрого запуска

[Шаблоны быстрого запуска Azure](https://azure.microsoft.com/resources/templates/) — это репозиторий для шаблонов Resource Manager. Вместо создания шаблона с нуля можно найти пример шаблона и настроить его. Шаблон, используемый в этом кратком руководстве, называется [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Создание стандартной учетной записи хранения). Шаблон определяет ресурс учетной записи службы хранилища Azure.

1. В Visual Studio Code выберите **Файл**>**Открыть файл**.
2. Скопируйте приведенный ниже URL-адрес и вставьте его в поле **Имя файла**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Чтобы открыть файл, выберите **Открыть**.
4. Выберите **Файл**>**Сохранить как**, чтобы сохранить файл в качестве **azuredeploy.json** на локальном компьютере.

## <a name="understand-the-schema"></a>Изучение схемы

1. Из VS Code сверните шаблон до корневого уровня. Имеется простейшая структура со следующими элементами.

    ![Простейшая структура шаблона Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

    * Параметр **$schema** указывает расположение файла схемы JSON, который описывает версию языка шаблона.
    * Параметр **contentVersion** задает этому элементу любое значение, чтобы документировать важные изменения в шаблоне.
    * Параметр **parameters** указывает значения, которые предоставляются при выполнении развертывания для настройки развертывания ресурсов.
    * Параметр **variables** указывает значения, используемые в виде фрагментов JSON в шаблоне для упрощения выражений на языке шаблона.
    * Параметр **resources** указывает типы ресурсов, которые развертываются или обновляются в группе ресурсов.
    * Параметр **outputs** указывает значения, возвращаемые после развертывания.

2. Разверните раздел **resources**. Вы увидите определенный ресурс `Microsoft.Storage/storageAccounts`. Этот шаблон создает незашифрованную учетную запись хранения.

    ![Определение учетной записи хранения в шаблоне Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resource.png)

## <a name="find-the-template-reference"></a>Поиск ссылки на шаблон

1. Перейти к [справочнику по шаблонам Azure](https://docs.microsoft.com/azure/templates/).
2. В поле **Фильтр по названию** введите **учетные записи хранения**.
3. Последовательно выберите **"Справочник", "Справочник по шаблонам", "Хранилище", &lt;"Версия">"Учетные записи хранения"** , как показано на приведенном ниже снимке экрана.

    ![Учетная запись хранения ссылки на шаблон Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

    Если вы не знаете, какую выбрать версию, используйте последнюю.

4. Найдите информацию о шифровании в определениях.

    ```json
    "encryption": {
      "services": {
        "blob": {
          "enabled": boolean
        },
        "file": {
          "enabled": boolean
        }
      },
      "keySource": "string",
      "keyvaultproperties": {
        "keyname": "string",
        "keyversion": "string",
        "keyvaulturi": "string"
      }
    },
    ```

    На той же веб-странице следующее описание подтверждает, что объект `encryption` используется для создания зашифрованной учетной записи хранения.

    ![Ссылка на шаблон Resource Manager: шифрование учетной записи хранения](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption.png)

    Ключом шифрования можно управлять двумя способами. Вы можете использовать ключи шифрования с шифрованием службы хранилища, управляемые корпорацией Майкрософт, или собственные ключи шифрования. Для простоты этого руководства мы используем параметр `Microsoft.Storage`, так что вам не нужно создавать Azure Key Vault.

    ![Ссылка на шаблон Resource Manager: объект шифрования учетной записи хранения](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption-object.png)

    Объект шифрования должен выглядеть следующим образом:

    ```json
    "encryption": {
        "services": {
            "blob": {
                "enabled": true
            },
            "file": {
              "enabled": true
            }
        },
        "keySource": "Microsoft.Storage"
    }
    ```

## <a name="edit-the-template"></a>Изменение шаблона

В Visual Studio Code измените шаблон, чтобы элемент ресурсов выглядел так:

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
> [Развертывание нескольких экземпляров](./resource-manager-tutorial-create-multiple-instances.md)
