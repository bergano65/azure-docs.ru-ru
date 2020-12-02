---
author: baanders
description: Включение файла для работы с Azure Digital Twins — предварительные требования для настройки экземпляра
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 8ea371e25f149c52412153aa0b9c3b7475dc77fd
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303579"
---
Для работы с Azure Digital Twins, как показано в этой статье, сначала нужно настроить **экземпляр Azure Digital Twins** и необходимые разрешения для его использования. Если у вас уже есть настроенный экземпляр Azure Digital Twins после предыдущих действий, можно использовать его.

В противном случае выполните инструкции по [настройке экземпляра и аутентификации](../articles/digital-twins/how-to-set-up-instance-portal.md). Кроме того, в этих инструкциях указано, как проверить, успешно ли завершен каждый шаг и готов ли новый экземпляр к работе.

После настройки экземпляра Azure Digital Twins запишите следующие значения, которые потребуются вам позднее при подключении к экземпляру:
* **_Имя узла_** экземпляра. Это значение можно найти на портале Azure ([инструкции](../articles/digital-twins/how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).
* **Подписка Azure**, которую вы использовали для создания экземпляра (можно использовать как имя, так и идентификатор). Узнать, в какой подписке находится экземпляр Azure Digital Twins, можно на той же странице *обзора* странице для экземпляра на [портале Azure](https://portal.azure.com).
