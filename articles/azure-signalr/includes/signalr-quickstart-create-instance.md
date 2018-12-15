---
title: включение файла
description: включение файла
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: c95f05127d62d366e1ad78b45df23d04ced0164a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53262803"
---
## <a name="create-an-azure-signalr-service-instance"></a>Создание экземпляра службы Azure SignalR

Приложение будет подключено к экземпляру службы SignalR в Azure.

1. Нажмите кнопку "Создать" в верхнем левом углу портала Azure. На экране "Создание" введите в поле поиска *Служба SignalR* и нажмите клавишу ВВОД.

    ![Поиск службы SignalR Azure](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. Выберите **Служба SignalR** в результатах поиска и щелкните **Создать**.

1. Введите следующие параметры.

    | Параметр      | Рекомендуемое значение  | ОПИСАНИЕ                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Имя ресурса** | Глобально уникальное имя | Имя, которое определяет новый экземпляр службы SignalR. Допустимые символы: `a-z`, `0-9` и `-`.  | 
    | **Подписка** | Ваша подписка | Подписка, в рамках которой создается экземпляр службы SignalR. | 
    | **[Группа ресурсов](../../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Имя новой группы ресурсов, в которой будет создан экземпляр службы SignalR. | 
    | **Местоположение.** | Запад США | Выберите ближайший [регион](https://azure.microsoft.com/regions/). |
    | **Ценовая категория** | Free | Поработайте со службой Azure SignalR бесплатно. |
    | **Число единиц** |  Не применяется | Число единиц определяет количество подключений, которые поддерживает ваш экземпляр службы SignalR. Это можно настроить только для цен. категории "Стандартный". |

    ![Создание службы SignalR](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Щелкните **Создать**, чтобы начать развертывание экземпляра службы SignalR.