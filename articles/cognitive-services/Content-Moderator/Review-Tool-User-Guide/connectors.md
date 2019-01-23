---
title: Подключение к другим службам при модерации содержимого — Content Moderator
titlesuffix: Azure Cognitive Services
description: Узнайте, как получить доступ к другим интерфейсам API для рабочих процессов Content Moderator с помощью соединителей.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 99d8b3603278a9c6c432ca32a1d85e9abe34e1da
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265589"
---
# <a name="connect-to-other-cognitive-services"></a>Подключение к другим службам Cognitive Services

Помимо интерфейсов API Content Moderator рабочие процессы Azure Content Moderator могут использовать другие интерфейсы API. Для доступа к другим интерфейсам API используется соответствующий соединитель в Content Moderator. Соединитель обеспечивает связь с другими интерфейсами API.

Content Moderator включает в себя следующие соединители по умолчанию:

* API распознавания эмоций
* API распознавания лиц
* облачная служба PhotoDNA.

![Доступные соединители Content Moderator](images/connectors-1.png)

## <a name="verify-your-credentials"></a>Проверка учетных данных 

Перед определением рабочего процесса убедитесь, что у вас есть действительные учетные данные для API соединителя, который вы хотите использовать.

1.  На панели мониторинга инструмента проверки выберите **Settings** (Параметры)  >  **Connectors** (Соединители).

  ![Выбор "Connectors" (Соединители) в Content Moderator](images/connectors-2.png)

2.  Щелкните значок **Edit** (Изменить) рядом с соединителем, учетные данные для которого требуется проверить.

  ![Выбор значка "Edit" (Изменить) в Content Moderator](images/connectors-3.png)

3.  Отобразится ключ подписки. Если вы внесли какие-либо изменения, по завершении щелкните **Save** (Сохранить).

  ![Страница "Edit Connector" (Изменение соединители) в Content Moderator](images/connectors-4-1.png)
 
## <a name="add-a-connector"></a>Добавление соединителя

1.  Перед добавлением соединителя необходимо получить ключ подписки. На панели мониторинга инструмента проверки выберите **Settings** (Параметры)  >  **Credentials** (Учетные данные). Выделите и скопируйте значение поля **Ocp-Admin-Subscription-Key**.

2.  Выберите **Соединители**. Выберите один из доступных соединителей, которые отображаются на панели мониторинга инструмента проверки. Затем щелкните **Connect** (Подключиться). 

  ![Страница "Add Connector" (Добавление соединителя) в Content Moderator](images/connectors-5.png)

3.  В поле **Ocp-Admin-Subscription-Key** вставьте ключ, который скопировали ранее. Затем нажмите кнопку **Сохранить**.

## <a name="next-steps"></a>Дополнительная информация

* Узнайте, как использовать соединители для [определения пользовательских рабочих процессов](workflows.md).
