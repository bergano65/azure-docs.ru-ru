---
title: Миграция из Google Maps | Документация Майкрософт
description: Руководство по переходу с карт Google на Microsoft Azure Maps. Руководство содержит инструкции по переключению на Azure Maps API-интерфейсы и пакеты SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6709c42b19b18c8cae783a6b4ecc2c0721e1217d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770311"
---
# <a name="migrate-from-google-maps"></a>Миграция из Google Карт

В этом учебнике содержатся сведения о переносе веб-приложений, мобильных и серверных программ из Google Maps на платформу карт Microsoft Azure. В этом учебнике содержатся сравнительные примеры кода, предложения по миграции и рекомендации по переходу на Azure Maps.

## <a name="azure-maps-platform-overview"></a>Обзор платформы Azure Maps

Azure Maps предоставляет разработчикам мощные геопространственные возможности, Упакованные с помощью регулярно обновляемых данных карт, чтобы обеспечить географический контекст для веб-и мобильных приложений. У Azure Maps есть совместимый с Azure One API-интерфейсов API для карт, поиска, маршрутизации, трафика, часовых поясов, географического расположения, геоограждения, данных сопоставления, погоды, мобильности и пространственных операций, поставляемых как для веб-приложений, так и для Android, а для разработки, обеспечения гибкости и переносимость на нескольких платформах.

## <a name="high-level-platform-comparison"></a>Сравнение платформ высокого уровня

В следующей таблице представлен высокоуровневый список функций Google Maps и относительная поддержка этих функций в Azure Maps. Этот список не включает дополнительные Azure Maps функции, такие как специальные возможности, API-интерфейсы геозоны, вычисление изохрон, пространственные операции, прямой доступ к плитке карты, пакетные службы и сравнение показателей покрытия данных (т. е. покрытие изображений).

| Функция Google Maps         | Поддержка Azure Maps                     |
|-----------------------------|:--------------------------------------:|
| веб-пакет SDK.                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | Запланировано                                |
| API-интерфейсы службы RESTFUL           | ✓                                      |
| Направления (маршрутизация)        | ✓                                      |
| Матрица расстояний             | ✓                                      |
| Elevation                   | Запланировано                                |
| Геокодирование (вперед и назад) | ✓                                      |
| Геолокация                 | Н/Д                                    |
| Поиск мест               | ✓                                      |
| Сведения о размещении              | Н/д — доступ к веб-сайту & номер телефона |
| Размещает фотографии               | Н/Д                                    |
| Разместить Автозаполнение          | ✓                                      |
| Статические карты                 | ✓                                      |
| Статическое представление улицы          | Н/Д                                    |
| Часовой пояс                   | ✓                                      |
| Внедренный API Maps           | Н/Д                                    |
| URL-адреса карт                    | Н/Д                                    |

В Google Maps предусмотрена обычная проверка подлинности на основе ключей. Azure Maps обеспечивает базовую проверку подлинности на основе ключей, а также высокую безопасность, Azure Active Directory проверку подлинности.

## <a name="licensing-considerations"></a>Вопросы лицензирования

При переходе на Azure Maps из Google Maps следует учитывать следующие моменты, касающиеся лицензирования.

- Azure Maps плата за использование интерактивных карт в зависимости от числа загруженных плиток карты, в то время как в Google Maps взимается загрузка элемента управления карты. В интерактивных Azure Maps SDK плитки карт кэшируются автоматически, чтобы снизить затраты на разработчика. Одна Azure Mapsная транзакция создается для каждых 15 плиток карт. Интерактивные пакеты SDK Azure Maps используют плитки 512 пикселей, а в среднем — одну или менее транзакций на одно представление страницы.
- Часто гораздо более экономично заменить статические изображения карты из веб-служб Google Maps на Azure Maps веб-пакет SDK, так как в этом случае используются плитки карты, и если пользователь не променяет и не масштабирует карту, они часто создают только часть транзакции на каждую нагрузку карты. Azure Maps веб-пакет SDK имеет параметры для отключения панорамирования и масштабирования. Кроме того, Azure Maps веб-пакет SDK предоставляет гораздо больше вариантов визуализации, чем делает веб-служба статического соответствия.
- Azure Maps позволяет хранить данные своей платформы в Azure. Его также можно кэшировать в любом расположении в течение шести месяцев согласно [условиям использования](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Ниже приведены некоторые связанные ресурсы для Azure Maps.

- [Страница цен Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Калькулятор цен Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps).
- [Azure Maps условия использования](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (входит в состав служб Microsoft Online Services)
- [Выберите нужную ценовую категорию в Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>Предлагаемый план миграции

Ниже приведен план высокого уровня миграции.

1. Проведите инвентаризацию того, какие пакеты SDK и службы Google Maps использует ваше приложение, и убедитесь, что Azure Maps предоставляет альтернативные пакеты SDK и службы для перехода на.
2. Создайте подписку Azure (если у вас ее еще нет) на [https://azure.com](https://azure.com).
3. Создайте учетную запись Azure Maps ([документацию](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)), ключ проверки подлинности или Azure Active Directory ([Документация](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)).
4. Перенос кода приложения.
5. Протестируйте перенесенное приложение.
6. Разверните перенесенное приложение в рабочей среде.

## <a name="azure-maps-technical-resources"></a>Azure Maps технические ресурсы

Ниже приведен список полезных технических ресурсов для Azure Maps.

- Обзор: [https://azure.com/maps](https://azure.com/maps)
- Документация: [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Примеры кода веб-пакета SDK: [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- Форумы для разработчиков: [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- Видео: [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- Блог: [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Отзывы Azure Maps (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)

## <a name="migration-support"></a>Поддержка миграции

Разработчики могут найти поддержку миграции на [форумах](https://aka.ms/AzureMapsForums) или с помощью одного из многих вариантов поддержки Azure: [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как перенести приложение Google Maps в следующие статьи:

> [!div class="nextstepaction"]
> [Перенос веб-приложения](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [Перенос приложения Android](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [Перенос веб-службы](migrate-from-google-maps-web-services.md)
