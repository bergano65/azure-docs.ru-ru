---
title: 'Использование интерфейса командной строки Databricks через Azure Cloud Shell '
description: Узнайте, как использовать интерфейс командной строки (CLI) в модулях Azure Cloud Shell для выполнения операций с Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: efb0d3222bfd98b15502163979425d47fa459e07
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605720"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Использование интерфейса командной строки Databricks через Azure Cloud Shell

Сведения об использовании интерфейса командной строки Databricks через Azure Cloud Shell при работе в Databricks.

## <a name="prerequisites"></a>Предварительные требования

* Рабочее пространство и кластер в Azure Databricks. Инструкции см. в статье [Начало работы с Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Настройте личный маркер доступа в Databricks. Инструкции см. в разделе об [управлении маркерами](/azure/databricks/dev-tools/api/latest/authentication).

## <a name="use-the-azure-cloud-shell"></a>Использование Azure Cloud Shell

1. Войдите на [портал Azure](https://portal.azure.com).
 
2. В правом верхнем углу выберите значок **Cloud Shell**.

   ![Запустить Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Запуск Azure Cloud Shell")

3. Убедитесь, что для среды Cloud Shell выбран вариант **Bash**. Как показано на следующем снимке экрана, эту среду можно выбрать в раскрывающемся списке.

   ![Выберите bash для среды Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Выберите Bash.") 

4. Создайте виртуальную среду, в которой можно будет установить интерфейс командной строки Databtricks. Используя фрагмент кода ниже, создайте виртуальную среду, которая называется `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Переключитесь на созданную вами виртуальную среду.

       source databrickscli/bin/activate

6. Установите интерфейс командной строки Databricks.

       pip install databricks-cli

7. Установите проверку подлинности с помощью Databricks, используя созданный маркер доступа, который был перечислен в списке необходимых компонентов. Используйте следующую команду:

       databricks configure --token

    Отобразятся следующие запросы.

    * Сначала появится запрос на указание узла Databricks. Введите значение в формате `https://eastus2.azuredatabricks.net`. Здесь **East US 2** — это регион в Azure, в котором была создана рабочая область Azure Databricks.

    * Далее появится запрос на ввод маркера. Введите маркер, который был создан ранее.

После выполнения этих действий можно начинать использовать интерфейс командной строки Databricks в Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Использование интерфейса командной строки Databricks

Теперь интерфейс командной строки Databricks можно использовать. Выполните, к примеру, следующую команду, чтобы вывести список всех кластеров Databricks, которые присутствуют в вашей рабочей области.

    databricks clusters list

Чтобы получить доступ к файловой системе Databricks (DBFS), можно использовать следующую команду:

    databricks fs ls


Полный справочник по командам см. в статье [Databricks CLI](/azure/databricks/dev-tools/databricks-cli) (Интерфейс командной строки Databricks).


## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения об интерфейсе командной строки Azure см. в статье [Обзор Azure Cloud Shell](../cloud-shell/overview.md)
* Список команд для интерфейса командной строки Azure см. в статье [Azure CLI reference](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest) (Справочные материалы по интерфейсу командной строки Azure)
* Список команд для интерфейса командной строки Databricks см.в статье [Databricks CLI](/azure/databricks/dev-tools/databricks-cli) (Интерфейс командной строки Databricks)


