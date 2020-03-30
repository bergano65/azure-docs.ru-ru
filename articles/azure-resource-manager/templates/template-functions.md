---
title: Функции шаблонов
description: Описывает функции, используемые в шаблоне диспетчера ресурсов Azure для извлечения значений, работы со строками и числовыми значениями и получения сведений о развертывании.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 1d2789e59c091b4e6c39be48b83fe610a592abe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156265"
---
# <a name="arm-template-functions"></a>Функции шаблона ARM

В этой статье описаны все функции, которые можно использовать в шаблоне Azure Resource Manager (ARM). Для получения информации об использовании функций в шаблоне [см.](template-expressions.md)

Для создания собственных функций см. раздел об [определяемых пользователем функциях](template-syntax.md#functions).

Большинство функций работают одинаково при развертывании в группе ресурсов, подписке, группе управления или арендаторе. Несколько функций не могут быть использованы во всех областях. Они отмечены в списках ниже.

<a id="array" aria-hidden="true" />
<a id="coalesce" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="json" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-and-object-functions"></a>Функции массива и объекта

Resource Manager предоставляет ряд функций для работы с массивами и объектами.

* [Массива](template-functions-array.md#array)
* [coalesce](template-functions-array.md#coalesce)
* [concat](template-functions-array.md#concat)
* [Содержит](template-functions-array.md#contains)
* [createArray](template-functions-array.md#createarray)
* [Пустой](template-functions-array.md#empty)
* [Первый](template-functions-array.md#first)
* [Пересечения](template-functions-array.md#intersection)
* [Json](template-functions-array.md#json)
* [Последний](template-functions-array.md#last)
* [длина](template-functions-array.md#length)
* [Мин](template-functions-array.md#min)
* [max](template-functions-array.md#max)
* [Диапазон](template-functions-array.md#range)
* [Пропустить](template-functions-array.md#skip)
* [Принять](template-functions-array.md#take)
* [Союза](template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Функции сравнения

Resource Manager предоставляет ряд функций для выполнения сравнений в шаблонах.

* [equals](template-functions-comparison.md#equals)
* [Меньше](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [greater](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="deployment-value-functions"></a>Функции для параметров развертывания

Диспетчер ресурсов предоставляет следующие функции для получения значений из разделов шаблонов и значений, связанных с развертыванием:

* [Развертывания](template-functions-deployment.md#deployment)
* [Среды](template-functions-deployment.md#environment)
* [Параметры](template-functions-deployment.md#parameters)
* [Переменные](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Логические функции

Resource Manager предоставляет для работы с логическими условиями следующие функции:

* [И](template-functions-logical.md#and)
* [bool](template-functions-logical.md#bool)
* [if](template-functions-logical.md#if)
* [Не](template-functions-logical.md#not)
* [Или](template-functions-logical.md#or)

<a id="add" aria-hidden="true" />
<a id="copyindex" aria-hidden="true" />
<a id="div" aria-hidden="true" />
<a id="float" aria-hidden="true" />
<a id="int" aria-hidden="true" />
<a id="minint" aria-hidden="true" />
<a id="maxint" aria-hidden="true" />
<a id="mod" aria-hidden="true" />
<a id="mul" aria-hidden="true" />
<a id="sub" aria-hidden="true" />

## <a name="numeric-functions"></a>Числовые функции

Диспетчер ресурсов предоставляет следующие функции для работы с целыми числами:

* [добавление](template-functions-numeric.md#add)
* [copyIndex](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [Плавать](template-functions-numeric.md#float)
* [Int](template-functions-numeric.md#int)
* [Мин](template-functions-numeric.md#min)
* [max](template-functions-numeric.md#max)
* [Мод](template-functions-numeric.md#mod)
* [mul](template-functions-numeric.md#mul)
* [Sub](template-functions-numeric.md#sub)

<a id="extensionResourceId" aria-hidden="true" />
<a id="listkeys" aria-hidden="true" />
<a id="list" aria-hidden="true" />
<a id="providers" aria-hidden="true" />
<a id="reference" aria-hidden="true" />
<a id="resourcegroup" aria-hidden="true" />
<a id="resourceid" aria-hidden="true" />
<a id="subscription" aria-hidden="true" />
<a id="subscriptionResourceId" aria-hidden="true" />
<a id="tenantResourceId" aria-hidden="true" />

## <a name="resource-functions"></a>Функции для работы с ресурсами

Диспетчер ресурсов предоставляет следующие функции для получения значений ресурсов:

* [расширениеResourceId](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [listKeys](template-functions-resource.md#listkeys)
* [списокСекреты](template-functions-resource.md#list)
* [list*](template-functions-resource.md#list)
* [providers](template-functions-resource.md#providers)
* [Ссылки](template-functions-resource.md#reference)
* [resourceGroup](template-functions-resource.md#resourcegroup) - может использоваться только при развертывании в группе ресурсов.
* [resourceId](template-functions-resource.md#resourceid) - может использоваться в любой сфере, но допустимые параметры меняются в зависимости от сферы.
* [подписка](template-functions-resource.md#subscription) - может быть использована только при развертывании в группе ресурсов или подписке.
* [подпискаResourceId](template-functions-resource.md#subscriptionresourceid)
* [арендаторРесурсИд](template-functions-resource.md#tenantresourceid)

<a id="base64" aria-hidden="true" />
<a id="base64tojson" aria-hidden="true" />
<a id="base64tostring" aria-hidden="true" />
<a id="concat" aria-hidden="true" />
<a id="containsstring" aria-hidden="true" />
<a id="datauri" aria-hidden="true" />
<a id="datauritostring" aria-hidden="true" />
<a id="emptystring" aria-hidden="true" />
<a id="endswith" aria-hidden="true" />
<a id="firststring" aria-hidden="true" />
<a id="guid" aria-hidden="true" />
<a id="indexof" aria-hidden="true" />
<a id="laststring" aria-hidden="true" />
<a id="lastindexof" aria-hidden="true" />
<a id="lengthstring" aria-hidden="true" />
<a id="padleft" aria-hidden="true" />
<a id="replace" aria-hidden="true" />
<a id="skipstring" aria-hidden="true" />
<a id="split" aria-hidden="true" />
<a id="startswith" aria-hidden="true" />
<a id="string" aria-hidden="true" />
<a id="substring" aria-hidden="true" />
<a id="takestring" aria-hidden="true" />
<a id="tolower" aria-hidden="true" />
<a id="toupper" aria-hidden="true" />
<a id="trim" aria-hidden="true" />
<a id="uniquestring" aria-hidden="true" />
<a id="uri" aria-hidden="true" />
<a id="uricomponent" aria-hidden="true" />
<a id="uricomponenttostring" aria-hidden="true" />

## <a name="string-functions"></a>Строковые функции

Диспетчер ресурсов предоставляет следующие функции для работы со строками:

* [base64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [concat](template-functions-string.md#concat)
* [Содержит](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [Пустой](template-functions-string.md#empty)
* [концыС](template-functions-string.md#endswith)
* [Первый](template-functions-string.md#first)
* [Формат](template-functions-string.md#format)
* [Guid](template-functions-string.md#guid)
* [Indexof](template-functions-string.md#indexof)
* [Последний](template-functions-string.md#last)
* [Lastindexof](template-functions-string.md#lastindexof)
* [длина](template-functions-string.md#length)
* [newGuid](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [Заменить](template-functions-string.md#replace)
* [Пропустить](template-functions-string.md#skip)
* [Сплит](template-functions-string.md#split)
* [начинаетсяс](template-functions-string.md#startswith)
* [Строка](template-functions-string.md#string)
* [Подстроки](template-functions-string.md#substring)
* [Принять](template-functions-string.md#take)
* [в Толоуэр](template-functions-string.md#tolower)
* [Toupper](template-functions-string.md#toupper)
* [Обрезать](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [Uri](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)
* [utcnow](template-functions-string.md#utcnow)

## <a name="next-steps"></a>Дальнейшие действия

* Для описания разделов в шаблоне ARM [см.](template-syntax.md)
* Инструкции по объединению нескольких шаблонов см. в статье [Использование связанных шаблонов в Azure Resource Manager](linked-templates.md).
* Чтобы итерировать определенное количество раз при создании типа ресурса, [см. Создать несколько экземпляров ресурсов в azure Resource Manager.](copy-resources.md)
* Чтобы узнать, как развернуть созданный шаблон, просмотрите [развертывание приложения с шаблонами ARM](deploy-powershell.md)
