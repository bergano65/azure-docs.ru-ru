---
title: Общие сведения о публикации предложения модуля Azure IoT Edge | Документация Майкрософт
description: Общие сведения о процессе публикации предложения модуля IoT Edge в Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 5b4d4471d9c77b5d13dfd5f8c2e9394b1c2d2a87
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60910701"
---
# <a name="iot-edge-module-offer-publishing-overview"></a>Общие сведения о публикации предложения модуля IoT Edge

<table> <tr> <td>В этом разделе объясняется, как опубликовать новое предложение модуля Azure IoT Edge в <a href="https://azuremarketplace.microsoft.com">Microsoft Azure Marketplace</a>. Модуль IoT Edge представляет собой совместимый с Docker контейнер, подготовленный для запуска на устройстве IoT Edge. Модуль Azure IoT Edge представляет собой наименьшую единицу вычислительных ресурсов, которыми управляет IoT Edge. Эти модули могут содержать службы Azure или пользовательский код решения. </td> <td><img src="./media/iotedge-icon1.png"  alt="Azure IoT Edge module icon" /></td> </tr> </table>

## <a name="publishing-process"></a>Процесс публикации

Ниже перечислены основные этапы для публикации предложения модуля IoT Edge.

1. Создание предложения.<br> Укажите подробные сведения о предложении. К этим сведениям относятся: описание предложения, маркетинговые материалы, сведения о поддержке и характеристики ресурса.

2. Создание бизнес- и технических ресурсов.<br> Создайте бизнес-ресурсы (юридические документы и маркетинговые материалы) и технические ресурсы для связанного решения (образы модулей IoT Edge, размещенные в Реестре контейнеров Azure).

3. Создание номера SKU.<br> Создайте номера SKU, связанные с предложением. Уникальный номер SKU нужен для каждого публикуемого образа.

4. Сертификация предложения и его публикация. <br>Оформив предложение и выполнив технические требования, вы можете отправить предложение на публикацию. В ходе отправки начинается процесс публикации. Во время этого процесса решение тестируется, проверяется, сертифицируется и активируется в Azure Marketplace.

## <a name="parts-of-an-offer"></a>Части предложения

В статьях по следующих ссылках рассматриваются основные части предложения модуля IoT Edge.

- [Предварительные требования](./cpp-prerequisites.md) <br>В этой статье перечислены технические и бизнес-требования, необходимые для создания и публикации предложения модуля IoT Edge.
- [Подготовка технических ресурсов для модуля IoT Edge](./cpp-create-technical-assets.md) <br>В этой статье описывается подготовка технических ресурсов для модуля IoT Edge. Эти ресурсы должны соответствовать всем необходимым техническим критериям, которые нужно выполнить для публикации модуля IoT Edge в Azure Marketplace.
- [Создание предложения модуля IoT Edge](./cpp-create-offer.md) <br>В этой статье перечислены шаги, необходимые для создания записи предложения модуля IoT Edge с использованием [Портала Cloud Partner](https://cloudpartner.azure.com).
- [Публикация предложения модуля IoT Edge](./cpp-publish-offer.md)<br> В этой статье описывается отправка предложения для публикации в Azure Marketplace.

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с [техническими и бизнес-требованиями](./cpp-prerequisites.md), которые должны быть выполнены для публикации модуля IoT Edge в Microsoft Azure Marketplace.
