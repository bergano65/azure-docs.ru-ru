---
title: Номера SKU для образа контейнеров Azure | Azure Marketplace
description: Настройка номеров SKU для контейнера Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 5b69eea8ad7fd4c62925b50434b653118890e280
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823151"
---
# <a name="container-skus-tab"></a>Вкладка номеров SKU контейнера

Вкладка **Номера SKU** на странице **Новое предложение** позволяет создать один или несколько номеров SKU, чтобы связать их с новым предложением.  Вы можете применить несколько номеров SKU, чтобы различать варианты решения с разными наборами функций, моделями выставления счетов и другими характеристиками.

## <a name="sku-settings"></a>Параметры номера SKU

При создании нового предложения с ним не связан ни один номер SKU. Чтобы создать новый номер SKU, выполните следующие действия.

1. На вкладке "Номера SKU" выберите **Новый номер SKU**.

   ![Запрос нового номера SKU](./media/containers-sku-settings.png)

2. Укажите необходимые сведения о номере SKU и контейнере. Каждый номер SKU соответствует образу контейнера. Номер SKU состоит из двух частей:

    -   Метаданные номера SKU
    -   метаданные контейнера.


### <a name="sku-metadata"></a>Метаданные номера SKU

Метаданные номера SKU содержат отображаемые сведения об онлайн-магазине для списка контейнера.

![Метаданные номера SKU](./media/containers-sku-details.png)


### <a name="container-metadata"></a>метаданные контейнера.

В метаданных контейнера содержатся справочные сведения о репозитории образа внутри Реестра контейнеров Azure (ACR). Azure Marketplace скопирует этот образ в собственный общедоступный реестр и сделает его доступным для клиентов после сертификации. Все запросы от пользователей Azure для загрузки образов контейнеров из Azure Marketplace выполняются общедоступным реестром Azure Marketplace, а не ACR.

![метаданные контейнера.](./media/containers-image-repository.png)
    
**Сведения о репозитории образов** на предыдущем снимке экрана содержат следующие поля.  Обязательные поля помечены звездочкой (*).

-   **Идентификатор подписки\*** — идентификатор подписки Azure, в которой СОДЕРЖИТСЯ запись контроля доступа.
-   **Имя группы ресурсов\*** — имя группы ресурсов записи контроля доступа.
-   **Имя реестра\*** — имя записи контроля доступа.
-   **Имя репозитория\*** — имя репозитория. Задав это значение, вы не сможете его изменить. Используйте уникальное имя, чтобы избежать конфликта с другими предложениями в вашей учетной записи.
-   **Username\*** — имя пользователя (имя администратора), связанное с изображением записи контроля доступа.
-   **Password\*** — пароль, связанный с изображением записи контроля доступа.

    >[!NOTE]
    >Имя пользователя и пароль необходимы для того, чтобы убедиться, что участники имеют доступ к реестру ACR, указанному в процессе публикации.


### <a name="image-version"></a>Версия образа

При публикации образа контейнера можно также предоставить один или несколько тегов образа или хэш-код агента работоспособности системы.

**Тег образа\* или дайджест**
 
- Этот тег или хэш-код должен содержать тег `latest` и тег версии (например, начинаться со строки `xx.xx.xx-`, где xx — это число). Здесь должны быть [теги манифеста](https://github.com/estesp/manifest-tool) для нацеливания на несколько платформ. Все теги, указанные в теге манифеста, следует добавить в пакет для передачи. 
- С помощью тегов можно добавить несколько версий контейнера. Все теги манифеста (за исключением `latest`) должны начинаться с `X.Y-` или `X.Y.Z-`, где X, Y и Z обозначают целые числа. <br/> Например, если тег `latest` указывает на `1.0.1-linux-x64`, `1.0.1-linux-arm32` и `1.0.1-windows-arm32`, следует добавить сюда все эти теги.

>[!NOTE]
>Не забудьте добавить **тег тестирования** в образ, чтобы его можно было легко определить во время тестирования.


## <a name="next-steps"></a>Дальнейшие действия

Используйте вкладку [Marketplace](./cpp-marketplace-tab.md), чтобы создать описание предложения для Marketplace. 
