---
title: Что такое Azure Stack? | Документация Майкрософт
description: Azure Stack позволяет запускать службы Azure в вашем центре обработки данных.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/25/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.lastreviewed: 02/25/2019
ms.openlocfilehash: 65894ccd9514bce1d429b336f8bd5e6674048e65
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820269"
---
# <a name="what-is-azure-stack"></a>Что такое Azure Stack?

Microsoft Azure Stack – это гибридная облачная платформа, позволяющая доставлять службы Azure из центра обработки данных. Эта платформа предназначена для поддержки развития бизнес-требований. Платформа Azure Stack может поддерживать новые сценарии для современных приложений, таких как пограничные и отключенные среды, или соответствовать определенным требованиям безопасности и законодательства.

Azure Stack предлагается в двух вариантах развертывания для соответствия вашим потребностям.


## <a name="azure-stack-development-kit"></a>Пакет средств разработки Azure Stack

[Пакет средств разработки Azure Stack (ASDK)](./asdk/asdk-what-is.md) – это развертывание Azure Stack с одним узлом, удобное для оценки и изучения Azure Stack.  Вы также можете использовать ASDK как среду разработки для разработки приложений с помощью API и средств, совместимых с Azure.

>[!Note]
>ASDK не предназначен для использования в качестве рабочей среды.

ASDK имеет такие ограничения:

* ASDK связан с одним поставщиком удостоверений Azure Active Directory (Azure AD) или служб федерации Active Directory (AD FS). В этом каталоге можно создать несколько пользователей и назначить им подписки.
* Так как компоненты Azure Stack развертываются на одном компьютере, для клиентских ресурсов доступны ограниченные физические ресурсы. Эта конфигурация не годится для масштабирования или оценки производительности.
* Сценарии сети ограничены из-за одного узла и требований к развертыванию сетевой карты.

## <a name="azure-stack-integrated-systems"></a>Интегрированные системы Azure Stack
Интегрированные системы Azure Stack предоставляются благодаря партнерским отношениям Майкрософт и [партнеров по оборудованию](https://azure.microsoft.com/overview/azure-stack/integrated-systems/). Это позволяет создать решение с инновациями облачного уровня и простотой управления вычислениями. Так как Azure Stack предлагается в качестве встроенного оборудования и программного обеспечения системы, у вас есть необходимые гибкость и элемент управления, а также возможность внедрять инновации облачного уровня. Интегрированные системы Azure Stack, размер которых варьируется от 4 до 16 узлов, совместно поддерживают партнер по оборудованию и корпорация Майкрософт.  Использование интегрированных систем Azure Stack позволяет создавать новые сценарии и развертывать новые решения для производственных рабочих нагрузок.

## <a name="next-steps"></a>Дополнительная информация

[Основные возможности и концепции](azure-stack-key-features.md)

[Azure Stack: An extension of Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/) (Azure Stack: расширение Azure), PDF
