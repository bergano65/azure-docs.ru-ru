---
title: Включение записных книжек в учетной записи Azure Cosmos DB (Предварительная версия)
description: Встроенные записные книжки Azure Cosmos DB позволяют анализировать и визуализировать данные с помощью портала. В этой статье описывается, как включить эту функцию для учетных записей Cosmos.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: c869069e40780b1fd399758e84b0ffba311398e3
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334197"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Включение записных книжек для учетных записей Azure Cosmos DB (Предварительная версия)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Встроенные записные книжки для Azure Cosmos DB в настоящее время доступны в следующих регионах Azure: Восточная Австралия, восток США, Восточная часть США 2, Северная Европа, Юго-Центральный регион США, Юго-Восточная Азия, южная часть Соединенного Королевства, Западная Европа и Западная часть США 2. Чтобы использовать записные книжки, [Создайте новую учетную запись с записными книжками](#enable-notebooks-in-a-new-cosmos-account) или [включите записные книжки для существующей учетной записи](#enable-notebooks-in-an-existing-cosmos-account) в одном из этих регионов.

Встроенные записные книжки Jupyter в Azure Cosmos DB позволяют анализировать и визуализировать данные с портала Azure. В этой статье описывается, как включить эту функцию для учетной записи Azure Cosmos DB.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Включение записных книжек в новой учетной записи Cosmos

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Последовательно выберите **Создать ресурс** > **Базы данных** > **Azure Cosmos DB**.
1. На странице **Создание учетной записи Azure Cosmos DB** выберите **записные книжки**. 
 
    :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks.png" alt-text="Выберите пункт &quot;записные книжки&quot; в колонке Azure Cosmos DB создание":::

1. Выберите **Review + create** (Просмотреть и создать). Можно пропустить параметр **сеть** и **теги** . 
1. Проверьте параметры учетной записи, а затем нажмите кнопку **Создать**. Создание учетной записи занимает несколько минут. Дождитесь, пока на странице портала появится сообщение **Развертывание выполнено**. 

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Область Уведомления на портале Azure":::

1. Выберите **Перейти к ресурсу** , чтобы перейти на страницу учетной записи Azure Cosmos DB.

   :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="Страница учетной записи Azure Cosmos DB":::

1. Перейдите в область **Обозреватель данных** . Теперь вы должны увидеть рабочую область записных книжек.

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="Создание рабочей области записных книжек Azure Cosmos DB":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Включение записных книжек в существующей учетной записи Cosmos

Вы также можете включить записные книжки на существующих учетных записях. Этот шаг необходимо выполнить только один раз для каждой учетной записи.

1. Перейдите в область **Обозреватель данных** учетной записи Cosmos.
1. Выберите **включить записные книжки**.

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="Создайте новую рабочую область записных книжек в обозреватель данных":::

1. Появится запрос на создание новой рабочей области записных книжек. Выберите **завершить установку.**
1. Теперь ваша учетная запись включена для использования записных книжек.

## <a name="create-and-run-your-first-notebook"></a>Создание и запуск первой записной книжки

Чтобы проверить возможность использования записных книжек, выберите одну из записных книжек в разделе образцы записных книжек. Это позволит сохранить копию записной книжки в рабочей области и открыть ее.

В этом примере мы будем использовать **GettingStarted. ipynb**. 

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="Просмотр записной книжки GettingStarted. ipynb":::

Чтобы запустить записную книжку, сделайте следующее:
1. Выберите первую ячейку кода, содержащую код Python. 
1. Выберите **выполнить** , чтобы запустить ячейку. Для запуска ячейки можно также использовать **сочетание клавиш Shift + Ввод** .
1. Обновите область ресурсов, чтобы увидеть созданную базу данных и контейнер.

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="Запустить записную книжку &quot;Начало работы&quot;":::

Можно также выбрать пункт **создать записную книжку** , чтобы создать новую записную книжку или передать существующий файл записной книжки (ipynb), выбрав команду **отправить файл** в меню **Мои записные книжки** . 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="Создание или передача новой записной книжки":::

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о преимуществах использования [записных книжек Jupyter в Azure Cosmos DB](cosmosdb-jupyter-notebooks.md).
