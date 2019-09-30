---
title: Создание пользовательских действий и ресурсов в Azure
description: В этом учебнике описано, как создавать настраиваемые действия и ресурсы в Azure Resource Manager. Он также продемонстрирует, как настраиваемые рабочие процессы взаимодействуют с шаблонами Azure Resource Manager, Azure CLI, Политикой Azure и журналами действий Azure.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: dc1601e344c371a5f0feaadd272a2c6ff40af031
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172939"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Создание пользовательских действий и ресурсов в Azure

Настраиваемым поставщиком называется контракт между Azure и конечной точкой. Настраиваемые поставщики позволяют настраивать рабочие процессы в Azure путем добавления новых API в Azure Resource Manager. Resource Manager с этими пользовательскими API может использовать новые возможности для развертывания и управления.

В этом учебнике описывается простой пример добавления новых действий и ресурсов в Azure и их интеграции.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Настройка Функций Azure для настраиваемых поставщиков Azure

Первая часть этого учебника демонстрирует процесс настройки приложения-функции Azure для работы с настраиваемыми поставщиками.

- [Настройка Функций Azure для настраиваемых поставщиков Azure](./tutorial-custom-providers-function-setup.md)

Настраиваемые поставщики могут работать с любым общедоступным URL-адресом.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>Создание конечной точки RESTful для настраиваемых поставщиков

Вторая часть учебника описывает, как создать конечную точку RESTful для пользовательских поставщиков.

- [Создание конечной точки RESTful для настраиваемых поставщиков](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Создание и использование настраиваемого поставщика

Третья часть учебника описывает создание настраиваемых поставщиков и использование их настраиваемых действий и ресурсов.

- [Создание и использование настраиваемого поставщика](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Дополнительная информация

Из этой статьи вы узнаете о настраиваемых поставщиках и их создании. Переходите к следующей части [Учебник: Настройка Функций Azure для настраиваемых поставщиков Azure](./tutorial-custom-providers-function-setup.md).

Дополнительные сведения приведены в следующих статьях:

- [Краткое руководство по созданию настраиваемого поставщика и развертыванию настраиваемых ресурсов](./create-custom-provider.md)
- [How to: Добавление настраиваемых действий в Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [How to: Добавление настраиваемых ресурсов в Azure REST API](./custom-providers-resources-endpoint-how-to.md)
