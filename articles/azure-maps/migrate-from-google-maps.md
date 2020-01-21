---
title: Руководство. Переход с Google Карт на Azure Maps | Microsoft Azure Maps
description: Руководство по переходу с Google Карт на Microsoft Azure Maps. Это руководство содержит инструкции по переходу на API-интерфейсы и пакеты средств разработки Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 0e841b1f386d45ddb4af8598855d8e739750307e
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910742"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Переход с Google Карт на Azure Maps

В этом руководстве содержатся сведения о переносе веб-приложений, мобильных и серверных программ из Google Карт на платформу Microsoft Azure Maps. Руководство предоставляет примеры кода для сравнения, а также предложения и рекомендации по переходу на Azure Maps.

## <a name="azure-maps-platform-overview"></a>Обзор платформы Azure Maps

Azure Maps предоставляет разработчикам всех направлений мощные геопространственные функции в сочетании с регулярно обновляемыми картографическими данными. Это позволяет предоставлять географический контекста для веб-приложений и мобильных приложений. Azure Maps содержит совместимый с API Azure One набор интерфейсов REST API для служб карт, поиска, построения маршрутов, отслеживания дорожного движения, часовых поясов, геолокации, геозон и данных карт, погоды и мобильности, а также службы "Пространственные операции", с поддержкой пакетов средств разработки для Интернета и Android. Это решение делает разработку простой и гибкой, а также обеспечивает возможность переноса на разные платформы.

## <a name="high-level-platform-comparison"></a>Обобщенное сравнение платформ

В следующей таблице представлен обобщенный список функций Google Карт и сведения о поддержке аналогичных функций в Azure Maps. В этом списке нет сведений о дополнительных функциях Azure Maps, например о специальных возможностях, API-интерфейсах геозон, вычислениях изохроны, пространственных операциях, прямом доступе к плитке карты, пакетных службах и сравнении показателей покрытия данных (т. е. покрытие изображений).

| Функция Google Карт         | Поддержка в Azure Maps                     |
|-----------------------------|:--------------------------------------:|
| веб-пакет SDK.                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | Запланировано                                |
| API-интерфейсы RESTful           | ✓                                      |
| Направления (построение маршрутов)        | ✓                                      |
| Матрица расстояний             | ✓                                      |
| Elevation                   | Запланировано                                |
| Геокодирование (прямое и обратное) | ✓                                      |
| Географическое положение                 | Недоступно                                    |
| Поиск мест               | ✓                                      |
| Сведения о местах              | Недоступно. Предоставляется адрес веб-сайта и номер телефона |
| Фотографии мест               | Недоступно                                    |
| Автозавершение мест          | ✓                                      |
| Статические карты                 | ✓                                      |
| Статическое представление улиц          | Недоступно                                    |
| Часовой пояс                   | ✓                                      |
| Внедренный API Карт           | Недоступно                                    |
| URL-адреса карт                    | Недоступно                                    |

Служба Google Карты использует базовую проверку подлинности на основе ключа. Azure Maps поддерживает не только базовую проверку подлинности на основе ключа, но и проверку подлинности Azure Active Directory с высоким уровнем безопасности.

## <a name="licensing-considerations"></a>Вопросы лицензирования

При переходе на Azure Maps с Google Карт следует учитывать следующие аспекты лицензирования.

- Azure Maps взимает плату за использование интерактивных карт в зависимости от числа загруженных плиток карты, тогда как в Google Картах оплачивается каждая загрузка элемента управления картой. В пакетах средств разработки для интерактивного использования Azure Maps плитки карт автоматически кэшируются, что позволяет снизить затраты разработчика. Одна транзакция Azure Maps создается для каждых 15 загруженных плиток карт. В пакетах средств разработки для интерактивного использования Azure Maps используются плитки размером 512 пикселей. Это дает в среднем не более одной транзакции на каждое представление страницы.
- Часто можно сократить затраты, используя вместо статических изображений карт из веб-служб Google Карты веб-пакет SDK для Azure Maps, так как в нем используются плитки карты. Если пользователь не применяет панорамирование или масштабирование карты, при каждой загрузке карты создается лишь небольшая часть одной транзакции. Веб-пакет SDK для Azure Maps позволяет отключить панорамирование и масштабирование. Кроме того, веб-пакет SDK для Azure Maps дает гораздо больше возможностей для визуализации, чем веб-служба статических карт.
- Azure Maps позволяет хранить в Azure данные для этой платформы. Также их можно кэшировать в любом расположении на срок до шести месяцев согласно [условиям использования](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Ниже перечислены некоторые ресурсы с информацией об Azure Maps.

- [Цены на Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Калькулятор цен Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps).
- [Условия использования Azure Maps](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (в составе условий для служб Microsoft Online Services)
- [Choose the right pricing tier in Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier) (Выбор подходящей ценовой категории для Azure Maps)

## <a name="suggested-migration-plan"></a>Предлагаемый план миграции

Далее описан обобщенный план миграции.

1. Составьте список пакетов SDK и служб Google Карты, которые используются в приложении, и убедитесь, что Azure Maps предоставляет пакеты SDK и службы с аналогичными возможностями.
2. Создайте подписку Azure (если у вас ее еще нет) на странице [https://azure.com](https://azure.com).
3. Создайте учетную запись Azure Maps ([документация](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) и ключ проверки подлинности или службу Azure Active Directory ([документация](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)).
4. Переместите код приложения.
5. Протестируйте перенесенное приложение.
6. Разверните перенесенное приложение в рабочей среде.

## <a name="azure-maps-technical-resources"></a>Технические ресурсы по Azure Maps

Ниже приведен список полезных технических ресурсов для Azure Maps.

- Общие сведения: [https://azure.com/maps](https://azure.com/maps)
- Документация: [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Примеры кода для веб-пакетов SDK: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Форумы разработчиков: [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Видео: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Блог: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Отзывы об Azure Maps (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)

## <a name="migration-support"></a>Поддержка миграции

Разработчики могут получить поддержку по миграции на [форумах](https://aka.ms/AzureMapsForums) или через любой из многих каналов поддержки Azure: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как перенести приложение Google Карт, изучив следующие статьи:

> [!div class="nextstepaction"]
> [Перенос веб-приложения](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Перенос приложения Android](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Перенос веб-службы](migrate-from-google-maps-web-services.md)
