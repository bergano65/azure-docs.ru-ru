---
title: Обновление кода до последней версии платформы | Документация Майкрософт
description: В этом разделе описано как обновить версию платформы Microsoft Dynamics 365 for Operations до последнего выпуска.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 787d9ef509bcafeb6a21d4b0ec952ea1eb4fd52b
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808014"
---
# <a name="upgrading-code-to-the-latest-platform"></a>Обновление кода до последней версии платформы

В этом разделе описано как обновить версию платформы Microsoft Dynamics 365 for Operations до последнего выпуска.

## <a name="overview"></a>Обзор

Платформа Microsoft Dynamics 365 for Operations состоит из компонентов приведенных ниже.

Двоичные файлы платформы Dynamics 365 for Operations, например, сервер прикладных объектов (AOS), платформа управления данными, отчетами и платформой бизнес-аналитики (BI), средства разработки и службы аналитики. Ниже приведены пакеты репозитория прикладных объектов (AOT).

1. Платформа приложения.
2. Приложения Foundation
3. Проверка основных компонентов

**Важно**. Чтобы перейти к последней версии платформы Dynamics 365 for Operations, Dynamics 365 для реализации операций не может иметь некоторые пользовательские изменения (overlayering) любого из пакетов AOT которые принадлежат к платформе. Это ограничение было введено в обновление платформы 3, так что платформе можно применить плавные непрерывные обновления. При запуске платформы, которая является более ранней, чем платформы с обновлением 3, см. раздел "Установка последнего обновления платформы в средах", ссылка на которую приведена ниже.

Дополнительные сведения об обновлении кода см. [здесь](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update).