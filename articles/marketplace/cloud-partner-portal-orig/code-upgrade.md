---
title: Обновление кода до последней версии платформы | Azure Marketplace
description: В этом разделе описано как обновить версию платформы Microsoft Dynamics 365 for Operations до последнего выпуска.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: aedc2c7474de0fe068a329eb2205e9bb08e62c3a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935284"
---
# <a name="upgrading-code-to-the-latest-platform"></a>Обновление кода до последней версии платформы

В этом разделе описано как обновить версию платформы Microsoft Dynamics 365 for Operations до последнего выпуска.

## <a name="overview"></a>Обзор

Платформа Microsoft Dynamics 365 for Operations состоит из компонентов приведенных ниже.

Двоичные файлы платформы Dynamics 365 for Operations, например, сервер прикладных объектов (AOS), платформа управления данными, отчетами и платформой бизнес-аналитики (BI), средства разработки и службы аналитики. Ниже приведены пакеты репозитория прикладных объектов (AOT).

1. Платформа приложения.
2. Приложения Foundation
3. Проверка основных компонентов

**Важно!** Чтобы перейти к последней Dynamics 365 для платформы операции, в Dynamics 365 для реализации операций не может иметь некоторые пользовательские изменения (с перекрытием) любого из AOT пакеты, которые принадлежат к платформе. Это ограничение было введено в обновление платформы 3, так что платформе можно применить плавные непрерывные обновления. При запуске платформы, которая является более ранней, чем платформы с обновлением 3, см. раздел "Установка последнего обновления платформы в средах", ссылка на которую приведена ниже.

Дополнительные сведения об обновлении кода см. [здесь](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update).