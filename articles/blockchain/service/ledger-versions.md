---
title: Служба Azure Blockchain поддерживаемые версии книги, исправления и обновления
description: Общие сведения о версиях поддерживаемых книг в службе Azure Blockchain, включая политику систем установкой исправлений и управляемые системой и управляемые пользователем обновления.
services: azure-blockchain
keywords: блокчейн;
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 53f65ec91a1e0f1e5a6322f0125bf83cd3e400b2
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399102"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Поддерживаемые версии службы Azure Blockchain книги

Использует служба Azure Blockchain, Ethereum под управлением [кворума](https://www.goquorum.com/developers) книги, предназначенных для обработки закрытый транзакций в пределах группы известных участников, служащее консорциума в службе Azure Blockchain.

В настоящее время поддерживает службы Azure Blockchain [кворума версии 2.2.1](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1) и [диспетчера транзакций Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Управление обновлениями

Управление версиями в кворума осуществляется с помощью основного и дополнительного и выпусков исправлений. Например если версия кворума 2.0.1, тип выпуска будет классифицировать следующим образом.

|Основной | Minor.  | Исправление  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Служба Azure Blockchain автоматически обновляет выпусков исправлений кворума к существующим элементам, работающей в течение 30 дней сделан доступным от кворума.

## <a name="availability-of-new-ledger-versions"></a>Доступности новых версий книги

Служба Azure Blockchain предоставляет последние основного и дополнительного номеров версии книги кворума в течение 60 дней, у изготовителя кворума. Для консорциумы можно выбрать при подготовке нового члена и консорциум предоставляются более четырех вспомогательные версии. В настоящее время обновление из до основной и дополнительной версии не поддерживается.

## <a name="next-steps"></a>Дальнейшие действия

[Ограничения в службе Azure Blockchain](limits.md)
