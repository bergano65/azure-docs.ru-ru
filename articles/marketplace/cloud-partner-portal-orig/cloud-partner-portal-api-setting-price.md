---
title: Цены на предложения виртуальных машин | Документация Майкрософт
description: В этом разделе описывается три способа указания цены для предложений виртуальных машин.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: c78a54d5002972339994d9590c0a3e23b5c69bd9
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807871"
---
<a name="pricing-for-virtual-machine-offers"></a>Цены на предложения виртуальных машин
==================================

Существует три способа выбрать политику ценообразования для предложений виртуальных машин: настроить цену за ядро, цены на каждое ядро и цены на электронную таблицу.


<a name="customized-core-pricing"></a>Настроенные цены за ядро
-----------------------

Цены каждого сочетания региона и ядра разные. В разделе определения **virtualMachinePricing**/**regionPrices** необходимо указать каждый регион из списка пользователя.  В запросе используйте верный код валюты для каждого [региона](#regions).  В следующем примере показаны эти требования.

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 2,
                    "1core": 2,
                    "2core": 3,
                    "4core": 4,
                    "6core": 5,
                    "8core": 2,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 4,
                    "36core": 4,
                    "40core": 4,
                    "64core": 4,
                    "128core": 4
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>Цены на каждое ядро
----------------

В этом случае издатели указывают одну цену в долларах США для номера SKU а все другие цены создаются автоматически. Цена на ядро задаются в **едином** параметре запроса.

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```


<a name="spreadsheet-pricing"></a>Цены на электронную таблицу
-------------------

Издатель также может отправить электронную таблицу цен у временное хранилище, а затем включить URI в запросе, как и другие артефакты файла. Электронную таблицу передают, преобразуют для оценки указанного ценового плана и обновляют предложение, используя сведения о ценах. Последующие запросы GET для предложения вернут URI электронной таблицы и установленные цены для региона.

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

<a name="regions"></a>регионы
-------

В следующей таблице показаны разные регионы, которые можно указать для настраиваемых цен за ядро, и их соответствующие коды валют.

| **Регион** | **Имя**             | **Код валюты** |
|------------|----------------------|-------------------|
| DZ         | Алжир              | DZD               |
| AR         | Аргентина            | ARS               |
| AU         | Австралия            | AUD               |
| AT         | Австрия              | EUR               |
| BH         | Бахрейн              | BHD               |
| BY         | Беларусь              | RUB               |
| BE         | Бельгия              | EUR               |
| BR         | Бразилия               | USD               |
| BG         | Болгария             | BGN               |
| CA         | Канада               | CAD               |
| CL         | Чили                | CLP               |
| CO         | Колумбия             | КОП               |
| CR         | Коста-Рика           | CRC               |
| HR         | Хорватия              | HRK               |
| CY         | Кипр               | EUR               |
| CZ         | Чешская Республика       | CZK               |
| DK         | Дания              | DKK               |
| DO         | Доминиканская Республика   | USD               |
| EC         | Эквадор              | USD               |
| EG         | Египет                | EGP               |
| SV         | Эль-Сальвадор          | USD               |
| EE         | Эстония              | EUR               |
| FI         | Финляндия              | EUR               |
| СВ         | Франция               | EUR               |
| DE         | Германия              | EUR               |
| GR         | Греция               | EUR               |
| GT         | Гватемала            | GTQ               |
| HK         | Гонконг, САР        | HKD               |
| HU         | Венгрия              | HUF               |
| IS         | Исландия              | ISK               |
| IN         | Индия                | INR               |
| ИД         | Индонезия            | IDR               |
| IE         | Ирландия              | EUR               |
| IL         | Израиль               | ILS               |
| IT         | Италия                | EUR               |
| JP         | Япония                | JPY               |
| JO         | Иордания               | JOD               |
| KZ         | Казахстан           | KZT               |
| KE         | Кения                | KES               |
| KR         | Корея                | KRW               |
| KW         | Кувейт               | KWD               |
| LV         | Латвия               | EUR               |
| LI         | Лихтенштейн        | CHF               |
| LT         | Литва            | EUR               |
| LU         | Люксембург           | EUR               |
| MK         | Македония (БЮРМ)       | MKD               |
| MY         | Малайзия             | MYR               |
| MT         | Мальта                | EUR               |
| MX         | Мексика               | MXN               |
| ME         | Черногория           | EUR               |
| MA         | Марокко              | MAD               |
| NL         | Нидерланды          | EUR               |
| NZ         | Новая Зеландия          | NZD               |
| NG         | Нигерия              | NGN               |
| НЕТ         | Норвегия               | NOK               |
| OM         | Оман                 | OMR               |
| PK         | Пакистан             | PKR               |
| PA         | Панама               | USD               |
| PY         | Парагвай             | PYG               |
| PE         | Перу                 | PEN               |
| PH         | Филиппины          | PHP               |
| PL         | Польша               | PLN               |
| PT         | Португалия             | EUR               |
| PR         | Пуэрто-Рико          | USD               |
| QA         | Катар                | QAR               |
| RO         | Румыния              | RON               |
| RU         | Россия               | RUB               |
| SA         | Саудовская Аравия         | SAR               |
| RS         | Сербия               | RSD               |
| SG         | Сингапур            | SGD               |
| SK         | Словакия             | EUR               |
| SI         | Словения             | EUR               |
| ZA         | ЮАР         | ZAR               |
| ES         | Испания                | EUR               |
| LK         | Шри-Ланка            | USD               |
| SE         | Швеция               | SEK               |
| CH         | Швейцария          | CHF               |
| TW         | Тайвань               | TWD               |
| ВП         | Таиланд             | THB               |
| TT         | Тринидад и Тобаго  | TTD               |
| TN         | Тунис              | TND               |
| TR         | Турция               | TRY               |
| UA         | Украина              | UAH               |
| AE         | Объединенные Арабские Эмираты | EUR               |
| GB         | Великобритания       | GBP               |
| США         | США        | USD               |
| UY         | Уругвай              | UYU               |
| VE         | Венесуэла            | USD               |
|  |  |  |
