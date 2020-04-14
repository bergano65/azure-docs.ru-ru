---
title: Ссылка на API портала облачных партнеров (ru) Лазурный рынок
description: Описание предварительных требований и список операций Marketplace API.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: b9f698ea81830aaa8761c05012cf6843d07ad5a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256422"
---
# <a name="cloud-partner-portal-api-reference"></a>Справочные материалы по API портала Cloud Partner

> [!NOTE]
> AA-аДИ облачного partner Portal интегрированы с Партнерским центром и продолжат работать после того, как ваши предложения будут перенесены в Партнерский центр. Интеграция вносит небольшие изменения. Просмотрите изменения в [API CPP,](#changes-to-cpp-apis-after-the-migration-to-partner-center) перечисленные в этом документе, чтобы убедиться, что ваш код продолжает работать после миграции в Центр Партнеров.

REST API портала Cloud Partner позволяют осуществлять программный поиск и обработку рабочих нагрузок, предложений и профилей издателей. API-интерфейсы используют управление доступом на основе ролей (RBAC) для обеспечения правильных разрешений во время обработки.

Эта ссылка предоставляет технические сведения для REST API портала Cloud Partner. Примеры полезных данных в этом документе, предназначены только для справки и могут меняться по мере добавления новых функциональных возможностей.

## <a name="prerequisites-and-considerations"></a>Предварительные требования и рекомендации

Прежде чем использовать API-интерфейсы, следует проверить следующее:

- статью [Предварительные требования](./cloud-partner-portal-api-prerequisites.md), чтобы узнать, как добавить субъект-службу к учетной записи и получить маркер доступа Azure Active Directory (Azure AD) для аутентификации;
- Две [стратегии валютного контроля,](./cloud-partner-portal-api-concurrency-control.md) доступные для вызова этих AA.
- дополнительные [рекомендации](./cloud-partner-portal-api-considerations.md) API, например, управление версиями и обработка ошибок.

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>Изменения в AAP после миграции в партнерский центр

| **API** | **Описание изменений** | **Влияние** |
| ------- | ---------------------- | ---------- |
| POST Опубликовать, GoLive, Отменить | Для мгновенных предложений заголовок ответа будет иметь другой формат, но будет продолжать работать таким же образом, обозначая относительный путь для получения статуса операции. | При отправке любого из соответствующих запросов POST на предложение, заголовок Местоположения будет иметь один из двух форматов в зависимости от миграционного статуса предложения:<ul><li>Предложения, не связанные с миграцией<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Миграционные предложения<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Операция GET | Для типов предложений, которые ранее поддерживали поле «уведомление-электронная почта» в ответе, это поле будет обесточено и больше не возвращается для мутировавших предложений. | Для миграционных предложений мы больше не будем отправлять уведомления в список электронных писем, указанных в запросах. Вместо этого служба API будет согласовываться с процессом электронной почты в Partner Center для отправки электронных писем. В частности, уведомления будут отправлены на адрес электронной почты, установленный в разделе контактной информации Продавца в настройках вашей учетной записи в Partner Center, чтобы уведомить вас о ходе работы.<br><br>Пожалуйста, просмотрите адрес электронной почты, установленный в разделе контактной информации Продавца в настройках вашей [учетной записи](https://partner.microsoft.com/dashboard/account/management) в Partner Center, чтобы убедиться, что для уведомлений предоставляется правильная электронная почта.  |

## <a name="common-tasks"></a>Стандартные задачи

В этой статье содержатся сведения о API, для выполнения следующих задач.

### <a name="offers"></a>Предложения

- [Получение всех предложений](./cloud-partner-portal-api-retrieve-offers.md)
- [Получение определенного предложения](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [Получение состояния предложения](./cloud-partner-portal-api-retrieve-offer-status.md)
- [Создать предложение](./cloud-partner-portal-api-creating-offer.md)
- [Публикация предложения](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Операции

- [Получение операций](./cloud-partner-portal-api-retrieve-operations.md)
- [Отмена операций](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Публикация приложения

- [Запуск](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Другие задачи

- [Настройка цен на предложения виртуальных машин](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Устранение неполадок

- [Устранение неполадок с аутентификацией](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
