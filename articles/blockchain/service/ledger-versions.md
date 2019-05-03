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
ms.openlocfilehash: 63c9a8b9e266dacbb0fb6faba50fb44ac9a4b46e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027890"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Поддерживаемые версии службы Azure Blockchain книги

Использует служба Azure Blockchain, Ethereum под управлением [кворума](https://github.com/jpmorganchase/quorum/wiki) книги, предназначенных для обработки закрытый транзакций в пределах группы известных участников, служащее консорциума в службе Azure Blockchain.

В настоящее время поддерживает службы Azure Blockchain [кворума версии 2.2.1](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1) и [диспетчера транзакций Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Управление обновлениями

Управление версиями в кворума осуществляется с помощью основного выпуска, важный момент выпуска и выпуска несущественным. Например если кворум версии 2.0.1, 2 — важный выпуск, 0 — это важный момент выпуск и 1 является несущественным выпуска. Служба будет автоматически устанавливать исправления незначительных доработанные выпуски книги. В настоящее время обновление основной и основных точечные выпуски не поддерживаются.

## <a name="availability-of-new-ledger-versions"></a>Доступности новых версий книги

Служба Azure Blockchain предоставляет последние версии книги в течение 60 дней, у изготовителя книги. Максимум четыре основных точечные выпуски предоставляются для консорциумы можно выбрать при подготовке нового члена и консорциум.

## <a name="next-steps"></a>Дальнейшие действия

[Ограничения в службе Azure Blockchain](limits.md)
