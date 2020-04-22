---
title: Руководство по публикации предложения "Контейнеры" в Azure Marketplace
description: В этой статье описываются требования к публикации предложения "Контейнеры" в Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 80bcf0d5d37a44cee2aab38161abac619542204a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684323"
---
# <a name="containers-offer-publishing-guide"></a>Руководство по публикации предложения "Контейнеры"

Предложение "Контейнеры" поможет опубликовать образ контейнера в Azure Marketplace. Сведения в этом руководстве помогут понять требования к этому предложению. 

Ниже приведены предложения транзакций с развертыванием и выставлением счетов через Marketplace. Пользователь видит призыв к действию — "Получить сейчас".

Используйте тип предложения "Контейнер", если ваше решение представляет собой образ контейнера Docker, подготовленный в виде службы контейнеров Azure на основе Kubernetes.

>[!NOTE]
>Например, это может быть "Служба Azure Kubernetes" или "Экземпляры контейнеров Azure" (самая популярная среди пользователей Azure в случае среды выполнения контейнеров на основе Kubernetes).  

В настоящее время корпорация Майкрософт поддерживает свободное лицензирование и модель с использованием собственной лицензии (BYOL).

## <a name="containers-offer"></a>Предложение "Контейнеры"

| Требование | Сведения |  
|:--- |:--- |  
| Выставление счетов и ценообразование | Поддерживается либо бесплатная, либо модель выставления счетов BYOL. |  
| Образ, созданный из файла Dockerfile | Образы контейнеров должны соответствовать спецификации образов Docker и создаваться из файла Dockerfile.<ul> <li>Дополнительные сведения о создании образов Docker см. в разделе об использовании по адресу [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Размещение в ACR | Образы контейнеров должны размещаться в репозитории в Реестре контейнеров Azure (ACR).<ul> <li>Дополнительные сведения о работе с ACR см. на странице "Краткое руководство. Создание реестра контейнеров на портале Azure" по адресу [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Добавление тегов к образам | У образа контейнера должен быть хотя бы один тег (но не более 16).<ul> <li>Дополнительные сведения о пометке образа тегами см. на странице тегов Docker по адресу [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>Следующие шаги

Если это еще не сделано, 

- [Узнайте](https://azuremarketplace.microsoft.com/sell) больше о рынке.

Чтобы зарегистрироваться в Партнерс-центре, начните создавать новое предложение или работать над существующим:

- [Войдите в Центр Партнеров,](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) чтобы создать или завершить свое предложение.
- Для получения дополнительной информации можно ознакомиться с [предложением контейнера Azure.](./partner-center-portal/create-azure-container-offer.md)
