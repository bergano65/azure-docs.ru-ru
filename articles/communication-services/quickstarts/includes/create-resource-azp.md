---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 11b9c553573d9e6188ba634b4cb966d6a9b850b4
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945937"
---
## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/dotnet/) бесплатно.

## <a name="create-azure-communication-resource"></a>Создание ресурса Служб коммуникации Azure

Чтобы создать ресурс Служб коммуникации Azure, сначала войдите на [портал Azure](https://portal.azure.com). Выберите **+ Создать ресурс** в левом верхнем углу страницы. 

:::image type="content" source="../media/create-a-communication-resource/create-resource-plus-sign.png" alt-text="Снимок экрана страницы портала Azure, на которой выделена кнопка "Создать ресурс"":::.

Введите **коммуникации** в поле **Поиск в Marketplace** или в строке поиска в верхней части портала.

:::image type="content" source="../media/create-a-communication-resource/searchbar-communication-portal.png" alt-text="Снимок экрана, на котором показан поиск Служб коммуникации в строке поиска.":::

В результатах выберите **Службы коммуникации**, а затем нажмите **Добавить**.

:::image type="content" source="../media/create-a-communication-resource/add-communication-portal.png" alt-text="Снимок экрана, на котором показана панель Azure с выделенной кнопкой "Добавить".":::

Теперь можно настроить ресурс Служб коммуникации. На первой странице процесса создания вам будет предложено указать следующее:

* Подписка
* Группа ресурсов (можно создать новую группу ресурсов или выбрать существующую)
* Имя ресурса Служб коммуникации
* Географический регион, с которым будет связан ресурс

На следующем шаге можно назначить ресурсу теги. Теги можно использовать для упорядочения ресурсов в Azure. Дополнительные сведения о тегах см. в [документации по маркировке ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-resources).

Наконец, можно проверить конфигурацию и **создать** ресурс. Обратите внимание, что развертывание займет несколько минут.

## <a name="manage-your-communication-services-resource"></a>Управление ресурсом Служб коммуникации

Для управления ресурсом Служб коммуникации перейдите на [портал Azure](https://portal.azure.com), а затем найдите и выберите **Службы коммуникации Azure**.

На странице **Службы коммуникации** выберите имя ресурса.

На странице **Обзор** ресурса можно выполнять базовые задачи управления: просмотр, завершение работы, запуск, перезагрузку и удаление. Дополнительные параметры конфигурации можно найти на странице ресурса в меню слева.