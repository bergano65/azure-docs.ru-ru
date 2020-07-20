---
title: Руководство по Переход с Google Карт на Azure Maps | Microsoft Azure Maps
description: Руководство по переходу с Google Карт на Microsoft Azure Maps. Это руководство содержит инструкции по переходу на API-интерфейсы и пакеты средств разработки Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 2422204a809e0b13f4e337d49b851a0338681853
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86249212"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>Переход с Google Карт на Azure Maps

В этом руководстве содержатся сведения о переносе веб-приложений, мобильных и серверных программ из Google Карт на платформу Microsoft Azure Maps. Руководство предоставляет примеры кода для сравнения, а также предложения и рекомендации по переходу на Azure Maps.

## <a name="azure-maps-platform-overview"></a>Обзор платформы Azure Maps

Azure Maps предоставляет разработчикам из всех отраслей мощные геопространственные возможности. Картографические данные регулярно обновляются, что позволяет обеспечить географический контекст для веб-приложений и мобильных приложений. Azure Maps имеет набор интерфейсов REST API, совместимых с Azure One API. Эти интерфейсы REST API предоставляют функции отрисовки карт, поиска, маршрутизации, передачи трафика, работы с учетом часовых поясов, геолокации, геозон, данных карт, прогнозирования погоды, мобильности и пространственных операций. Операции снабжены пакетами SDK для веб-приложений и Android, чтобы обеспечить простоту и гибкость разработки, а также возможность переноса кода на разные платформы.

## <a name="high-level-platform-comparison"></a>Обобщенное сравнение платформ

В таблице представлен обобщенный список функций Azure Maps, которые аналогичны функциям в Google Картах. В этом списке отображаются не все функции Azure Maps. Некоторые дополнительные функции Azure Maps включают специальные возможности, геозоны, вычисления изохроны, пространственные операции, прямой доступ к фрагменту карты, пакетные службы и сравнение показателей покрытия данных (т. е. покрытие изображений).

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
| Ближайшие дороги               | ✓                                      |
| Поиск мест               | ✓                                      |
| Сведения о местах              | Недоступно. Предоставляется адрес веб-сайта и номер телефона |
| Фотографии мест               | Недоступно                                    |
| Автозавершение мест          | ✓                                      |
| Привязка к дороге                | ✓                                      |
| Ограничения скорости                | ✓                                      |
| Статические карты                 | ✓                                      |
| Статическое представление улиц          | Недоступно                                    |
| Часовой пояс                   | ✓                                      |
| Внедренный API Карт           | Недоступно                                    |
| URL-адреса карт                    | Недоступно                                    |

Служба Google Карты использует базовую проверку подлинности на основе ключа. Azure Maps поддерживает не только базовую проверку подлинности на основе ключа, но и проверку подлинности Azure Active Directory. Проверка подлинности с помощью Azure Active Directory предоставляет больше функций обеспечения безопасности, чем базовая проверка подлинности на основе ключа.

## <a name="licensing-considerations"></a>Вопросы лицензирования

При переходе на Azure Maps из Google Карт учитывайте следующие моменты, связанные с лицензированием.

- За использование интерактивных карт Azure Maps взымает плату, основанную на количестве загруженных фрагментов карты. С другой стороны, служба Google Карты взимает плату за загрузку элемента управления картой. В пакетах средств разработки для интерактивного использования Azure Maps плитки карт автоматически кэшируются, что позволяет снизить затраты на разработку. Одна транзакция Azure Maps создается для каждых 15 загруженных плиток карт. В пакетах средств разработки для интерактивной работы с Azure Maps используются фрагменты размером 512 пикселей. Это дает возможность выполнять в среднем не более одной транзакции на каждое представление страницы.
- Зачастую гораздо экономичнее заменить статические изображения карты из веб-служб Google Карт на веб-пакет SDK Azure Maps. Веб-пакет SDK для Azure Maps использует фрагменты карт. Если пользователь не изменяет карту и не масштабирует ее, служба зачастую создает лишь часть транзакции на каждую загрузку карты. Веб-пакет SDK для Azure Maps позволяет отключить панорамирование и масштабирование при необходимости. Кроме того, веб-пакет SDK для Azure Maps дает гораздо больше возможностей для визуализации, чем веб-служба статических карт.
- Azure Maps позволяет хранить в Azure данные для этой платформы. Также данные можно кэшировать в любом расположении на срок до шести месяцев согласно [условиям использования](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46).

Ниже перечислены некоторые ресурсы с информацией об Azure Maps.

- [Цены на Azure Maps](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Калькулятор цен Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps).
- [Условия использования Azure Maps](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (в составе условий для служб Microsoft Online Services)
- [Choose the right pricing tier in Azure Maps](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier) (Выбор подходящей ценовой категории для Azure Maps)

## <a name="suggested-migration-plan"></a>Предлагаемый план миграции

Далее описан обобщенный план миграции.

1. Проведите инвентаризацию пакетов SDK Google Карт и служб, которые использует ваше приложение. Убедитесь, что Azure Maps предоставляет альтернативные службы и пакеты SDK.
2. Если у вас еще нету подписки Azure, создайте ее на странице [https://azure.com](https://azure.com).
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
- Технический блог: [https://aka.ms/AzureMapsTechBlog](https://aka.ms/AzureMapsTechBlog)
- Отзывы об Azure Maps (UserVoice): [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)
- [Jupyter Notebook для Azure Maps](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)

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
