---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 3ee9aca1ee86b04998e357875b98b14782b3e56d
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56963305"
---
### <a name="launch-the-publish-wizard"></a>Запуск мастера публикации

В **обозревателе решений** щелкните правой кнопкой мыши проект **SharingService** и выберите пункт **Опубликовать**.

Мастер публикации запустится автоматически. Выберите **Служба приложений** > **Опубликовать**, чтобы открыть диалоговое окно **Создать службу приложений**.

### <a name="sign-in-to-azure"></a>Вход в Azure

В диалоговом окне **Создание службы приложений** щелкните **Добавить учетную запись**, а затем войдите в подписку Azure. Если вы уже вошли, выберите нужную учетную запись из раскрывающегося списка.

> [!NOTE]
> Если вы уже выполнили вход, пока не нажимайте кнопку **Создать**.
>

### <a name="create-a-resource-group"></a>Создание группы ресурсов

[!INCLUDE [resource group intro text](resource-group.md)]

Рядом с **группой ресурсов** выберите команду **Создать**.

Присвойте группе ресурсов имя **myResourceGroup** и нажмите кнопку **ОК**.

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений

[!INCLUDE [app-service-plan](app-service-plan.md)]

Рядом с полем **План размещения** выберите **Создать**.

В диалоговом окне **Настроить план размещения** используйте параметры из таблицы.

| Параметр | Рекомендуемое значение | ОПИСАНИЕ |
|-|-|-|
|План обслуживания приложения| MySharingServicePlan | Имя плана службы приложений. |
| Расположение | Запад США | Центр обработки данных, где размещается веб-приложение. |
| Размер | Free | [Ценовая категория](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) определяет возможности размещения. |

Нажмите кнопку **ОК**.

### <a name="create-and-publish-the-web-app"></a>Создание и публикация веб-приложения

В поле **Имя приложения** введите уникальное имя (допустимые символы: `a-z`, `0-9` и `-`) или примите уникальное имя, созданное автоматически. URL-адрес веб-приложения: `https://<app_name>.azurewebsites.net`, где `<app_name>` — имя приложения.

Нажмите кнопку **Создать**, чтобы начать создавать ресурсы Azure.

Когда мастер завершит работу, веб-приложение ASP.NET Core будет опубликовано в Azure и запущено в браузере по умолчанию.

![Опубликованное веб-приложение ASP.NET в Azure](./media/spatial-anchors-azure/web-app-running-live.png)

Имя приложения, указанное [на этапе создания и публикации](#create-and-publish-the-web-app), используется как префикс URL-адреса в формате `https://<app_name>.azurewebsites.net`. Сохраните этот URL-адрес, так как он понадобится нам позднее.
