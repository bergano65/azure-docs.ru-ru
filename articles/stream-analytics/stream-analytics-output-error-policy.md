---
title: Политики обработки ошибок вывода в Azure Stream Analytics
description: Узнайте о том, какие политики обработки ошибок вывода доступны в Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 22112272bb302769e5969cf6995d486438deb41f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75431611"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Политика обработки ошибок вывода в Azure Stream Analytics
В этой статье описываются политики обработки ошибок вывода, которые могут быть настроены в Azure Stream Analytics.

Политики обработки ошибок вывода применяются только к ошибкам преобразования данных, возникающим, когда выходное событие, созданное заданием Stream Analytics, не соответствует схеме целевого приемника. Это политику можно настроить, выбрав параметр **Повторить** или **Удалить**. На портале Azure в задании Stream Analytics перейдите к разделу **Настройка** и щелкните **Политика обработки ошибок**, чтобы сделать выбор.

![Расположение политики обработки ошибок вывода в Azure Stream Analytics](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Повторить попытку
Когда происходит ошибка, Azure Stream Analytics бесконечно повторяет попытку записи события, пока запись не будет выполнена. Время ожидания для повторных попыток не задано. В конечном итоге обработку всех последующих событий блокирует событие, которое Azure Stream Analytics безуспешно пытается записать. Это политика обработки ошибок вывода по умолчанию.

## <a name="drop"></a>Drop
Azure Stream Analytics будет удалять все выходные события, вызывающее ошибку преобразования данных. Восстановить удаленные события для повторной обработки позже невозможно.


Для всех временных ошибок (например, сбоев сети) выполняется повторная попытка независимо от конфигурации политики обработки ошибок вывода.


## <a name="next-steps"></a>Дальнейшие шаги
[Руководство по устранению неполадок для Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
