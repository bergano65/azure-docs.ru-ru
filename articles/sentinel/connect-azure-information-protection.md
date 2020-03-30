---
title: Подключение защиты информации Azure к Azure Sentinel
description: Узнайте, как подключить данные о защите информации Azure в Azure Sentinel.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 396fd7c4289c9d02d451b26f5fb6299acd31e2a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588558"
---
# <a name="connect-data-from-azure-information-protection"></a>Подключение данных из Azure Information Protection

> [!IMPORTANT]
> Разъем данных По защите информации Azure в Azure Sentinel в настоящее время находится в открытом доступе.
> Эта функция предоставляется без соглашения об уровне обслуживания, и она не рекомендуется для производственных нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Вы можете передавать информацию о журналье из [Azure Information Protection](https://azure.microsoft.com/services/information-protection/) в Azure Sentinel, настраивая разъем данных Azure Information Protection. Защита информации Azure помогает контролировать и обезопасить конфиденциальные данные, независимо от того, хранятся они в облаке или в локальном помещении.

Если [центральная отчетность для Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) уже настроена таким образом, чтобы информация о журнале из этой службы хранилась в том же рабочем пространстве, что и в настоящее время для Azure Sentinel, можно пропустить конфигурацию этого разъема данных. Информация о причинах регистрации из Azure Information Protection уже доступна Для Azure Sentinel.

Однако, если информация о журнале из Azure Information Protection находится в другом рабочем пространстве Log Analytics, чем та, которую вы в настоящее время выбрали для Azure Sentinel, сделайте одно из следующих:

- Измените выбранное рабочее пространство в Azure Sentinel.

- Измените рабочее пространство для защиты информации Azure, что можно сделать, нанастройки этого разъема данных.
    
    При изменении рабочего пространства новые данные отчетности для защиты информации Azure теперь будут храниться в рабочей области, которую вы используете для Azure Sentinel, а исторические данные недоступны для Azure Sentinel. Кроме того, если предыдущее рабочее пространство настроено для пользовательских запросов, предупреждений или апогея REST, они должны быть перенастроены для рабочего пространства Azure Sentinel, если вы хотите продолжать использовать их для защиты информации Azure. Для клиентов и служб, пользующиеся защитой информации Azure, не требуется реконфигурация.

## <a name="prerequisites"></a>Предварительные требования

- Одна из следующих ролей администратора Azure AD для вашего клиента: 
    - Администратор защиты информации Azure
    - администратор безопасности;
    - администратор соответствия требованиям.
    - Администратор данных о соответствии
    - Глобальный администратор.
    
    > [!NOTE]
    > Вы не можете использовать роль администратора Azure Information Protection, если ваш арендатор находится на [единой платформе маркировки.](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)
    
    Эти роли администратора необходимы только для настройки разъема защиты информации Azure и не требуются при подключении Azure Sentinel к защите информации Azure.

- Разрешения на чтение и запись в рабочее пространство Log Analytics, используемую для защиты информации Azure Sentinel и Azure.

- Защита информации Azure была добавлена на портал Azure. Если вам нужна помощь в этом шаге, [перейдите на портал Azure.ru.](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal)

## <a name="connect-to-azure-information-protection"></a>Подключение к защите информации Azure

Используйте следующие инструкции, если вы еще не настроили рабочее пространство log Analytics для защиты информации Azure или вам нужно изменить рабочее пространство, в которое хранится информация о защите информации Azure.

1. В Azure Sentinel выберите **разъемы данных,** а затем **защиту информации Azure (Preview)**.

2. Выберите **страницу Open connector**.

3. В лезвии **аналитики настройки (Preview)** выберите рабочее пространство, которое в настоящее время используется для Azure Sentinel. При выборе другого рабочего пространства данные отчетности из Azure Information Protection будут недоступны для Azure Sentinel.

4. При выборе рабочего пространства выберите **OK** и теперь разъем **STATUS** должен быть преобразован в **Connected.**

5. Данные отчетности из Azure Information Protection хранятся в **таблице InformationProtectionLogs_CL** в выбранном рабочем пространстве. 
    
    Чтобы использовать соответствующую схему в Azure Monitor для этих данных отчетности, ищите **InformationProtectionEvents**. Для получения информации об этих функциях событий смотрите [ссылку на схему «Дружелюбная схема» для раздела функций событий](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) из документации по защите информации Azure.

## <a name="next-steps"></a>Дальнейшие действия

В этом документе вы узнали, как подключить защиту информации Azure к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
