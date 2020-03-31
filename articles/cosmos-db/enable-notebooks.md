---
title: Включить ноутбуки в учетной записи Azure Cosmos DB (предварительный просмотр)
description: Встроенные ноутбуки Azure Cosmos DB позволяют анализировать и визуализировать данные из портала. В этой статье описывается, как включить эту функцию для учетных записей Cosmos.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: dcec310db43baa513b2d574d03f3f35dee3f773b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768025"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Включить ноутбуки для учетных записей Azure Cosmos DB (предварительный просмотр)

> [!IMPORTANT]
> Встроенные ноутбуки для Azure Cosmos DB в настоящее время доступны в следующих регионах Azure: Австралия Восток, Восточная ЧАСТЬ США, Восточная ЧАСТЬ США 2, Северная Европа, южная часть Центральной Части США, юго-восточная Азия, Великобритания на юге, западная Европа и Западная ЧАСТЬ США 2. Для использования ноутбуков [создайте новую учетную запись с блокнотами](#enable-notebooks-in-a-new-cosmos-account) или [включите блокноты на существующей учетной записи](#enable-notebooks-in-an-existing-cosmos-account) в одном из этих регионов.

Встроенные ноутбуки Jupyter в Azure Cosmos DB позволяют анализировать и визуализировать данные с портала Azure. В этой статье описывается, как включить эту функцию для учетной записи Azure Cosmos DB.

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>Включить ноутбуки в новой учетной записи Космос
1. Войти на [портал Azure](https://portal.azure.com/).
1. Выберите **Создать ресурс** > **Базы данных** > **Azure Cosmos DB.**
1. На странице **«Создайте Azure Cosmos DB—200»** выберите **блокноты.** 
 
    ![Выберите опцию ноутбуков в Azure Cosmos DB Создать лезвие](media/enable-notebooks/create-new-account-with-notebooks.png)
1. Выберите **Review + create** (Просмотреть и создать). Вы можете пропустить опцию **Сети** и **Теги.** 
1. Проверьте параметры учетной записи, а затем нажмите кнопку **Создать**. Создание учетной записи занимает несколько минут. Дождитесь, пока на странице портала появится сообщение **Развертывание выполнено**. 

    ![Область "Уведомления" на портале Azure](media/enable-notebooks/create-new-account-with-notebooks-complete.png)
1. Выберите **Перейти к ресурсу**, чтобы перейти на страницу учетной записи Azure Cosmos DB. 

    ![Страница учетной записи Azure Cosmos DB](../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png)

1. Перейдите к панели **Data Explorer.** Теперь вы должны увидеть рабочее пространство ноутбуков.

    ![Новое рабочее пространство ноутбуков Azure Cosmos DB](media/enable-notebooks/new-notebooks-workspace.png)

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Включить ноутбуки в существующей учетной записи Космос
Вы также можете включить блокноты на существующих учетных записях. Этот шаг должен быть сделан только один раз на счет.

1. Перейдите к панели **Data Explorer** в учетной записи Cosmos.
1. Выберите **ноутбуки включить**.

    ![Создание нового рабочего пространства ноутбуков в Data Explorer](media/enable-notebooks/enable-notebooks-workspace.png)
1. Это подскажет вам создать новое рабочее пространство ноутбуков. Выберите **полную настройку.**
1. Теперь ваша учетная запись включена в использование ноутбуков!

## <a name="create-and-run-your-first-notebook"></a>Создайте и запустите свой первый блокнот

Чтобы убедиться, что вы можете использовать ноутбуки, выберите один из ноутбуков под образцами ноутбуков. Это позволит сохранить копию ноутбука в рабочее пространство и открыть его.

В этом примере мы будем использовать **GettingStarted.ipynb**. 

![Посмотреть ноутбук GettingStarted.ipynb](media/enable-notebooks/select-getting-started-notebook.png)

Для запуска ноутбука:
1. Выберите первую ячейку кода, содержащую код Python. 
1. Выберите **Выполнить** для запуска ячейки. Вы также можете использовать **Shift и Enter** для запуска ячейки.
1. Обновите панель ресурсов, чтобы увидеть созданную базу данных и созданный контейнер.

    ![Запуск запуска ноутбука](media/enable-notebooks/run-first-notebook-cell.png)

Вы также можете выбрать **новый ноутбук** для создания нового ноутбука или загрузить существующий блокнот (.ipynb) файл, выбрав **файл загрузки** из меню **My Notebooks.** 

![Создание или загрузка нового ноутбука](media/enable-notebooks/create-or-upload-new-notebook.png)

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте о преимуществах [ноутбуков Azure Cosmos DB Jupyter](cosmosdb-jupyter-notebooks.md)
