---
title: Функции шаблонов
description: Описывает функции, используемые в шаблоне диспетчера ресурсов Azure для извлечения значений, работы со строками и числовыми значениями и получения сведений о развертывании.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: a15e7dfdf01a99cd23b216fafcfb44320a716d16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231294"
---
# <a name="arm-template-functions"></a>Функции шаблонов ARM

В этой статье описываются все функции, которые можно использовать в шаблоне Azure Resource Manager (ARM). Сведения об использовании функций в шаблоне см. в разделе [синтаксис шаблона](template-expressions.md).

Для создания собственных функций см. раздел об [определяемых пользователем функциях](template-syntax.md#functions).

Большинство функций работают одинаково при развертывании в группе ресурсов, подписке, группе управления или клиенте. Некоторые функции нельзя использовать во всех областях. Они указаны в списках ниже.

<a id="array" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-functions"></a>Функции массива

Диспетчер ресурсов предоставляет несколько функций для работы с массивами.

* [inArray](template-functions-array.md#array)
* [сцеплен](template-functions-array.md#concat)
* [contains](template-functions-array.md#contains)
* [createArray](template-functions-array.md#createarray)
* [пустых](template-functions-array.md#empty)
* [началь](template-functions-array.md#first)
* [крайне](template-functions-array.md#intersection)
* [Последняя](template-functions-array.md#last)
* [length](template-functions-array.md#length)
* [минимум](template-functions-array.md#min)
* [max](template-functions-array.md#max)
* [разнообраз](template-functions-array.md#range)
* [сразу](template-functions-array.md#skip)
* [нимают](template-functions-array.md#take)
* [наборов](template-functions-array.md#union)

<a id="coalesce" aria-hidden="true" />
<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>Функции сравнения

Resource Manager предоставляет ряд функций для выполнения сравнений в шаблонах.

* [объединение](template-functions-comparison.md#coalesce)
* [equals](template-functions-comparison.md#equals)
* [столь](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [greater](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="date-functions"></a>Функции данных

Диспетчер ресурсов предоставляет следующие функции для работы с датами.

* [датетимеадд](template-functions-date.md#datetimeadd)
* [utcnow](template-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>Функции для параметров развертывания

Диспетчер ресурсов предоставляет следующие функции для получения значений из разделов шаблонов и значений, связанных с развертыванием:

* [развертывания](template-functions-deployment.md#deployment)
* [PXE](template-functions-deployment.md#environment)
* [Вход](template-functions-deployment.md#parameters)
* [среды](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>Логические функции

Resource Manager предоставляет для работы с логическими условиями следующие функции:

* [и](template-functions-logical.md#and)
* [bool](template-functions-logical.md#bool)
* [if](template-functions-logical.md#if)
* [недостаточно](template-functions-logical.md#not)
* [или диспетчер конфигурации служб](template-functions-logical.md#or)

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

* [add](template-functions-numeric.md#add)
* [copyIndex](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [float](template-functions-numeric.md#float)
* [int](template-functions-numeric.md#int)
* [минимум](template-functions-numeric.md#min)
* [max](template-functions-numeric.md#max)
* [взят](template-functions-numeric.md#mod)
* [mul](template-functions-numeric.md#mul)
* [Директор](template-functions-numeric.md#sub)

<a id="json" aria-hidden="true" />

## <a name="object-functions"></a>Функции объектов

Диспетчер ресурсов предоставляет несколько функций для работы с объектами.

* [contains](template-functions-object.md#contains)
* [пустых](template-functions-object.md#empty)
* [крайне](template-functions-object.md#intersection)
* [формат](template-functions-object.md#json)
* [length](template-functions-object.md#length)
* [наборов](template-functions-object.md#union)

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

* [екстенсионресаурцеид](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [listKeys](template-functions-resource.md#listkeys)
* [листсекретс](template-functions-resource.md#list)
* [числ](template-functions-resource.md#list)
* [поставщик](template-functions-resource.md#providers)
* [IsReference](template-functions-resource.md#reference)
* [resourceGroup](template-functions-resource.md#resourcegroup) — может использоваться только в развертываниях для группы ресурсов.
* [resourceId](template-functions-resource.md#resourceid) — может использоваться в любой области, но допустимые параметры изменяются в зависимости от области.
* [Подписка](template-functions-resource.md#subscription) — может использоваться только в развертываниях для группы ресурсов или подписки.
* [субскриптионресаурцеид](template-functions-resource.md#subscriptionresourceid)
* [тенантресаурцеид](template-functions-resource.md#tenantresourceid)

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

* [формате](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [сцеплен](template-functions-string.md#concat)
* [contains](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [пустых](template-functions-string.md#empty)
* [endsWith](template-functions-string.md#endswith)
* [началь](template-functions-string.md#first)
* [format](template-functions-string.md#format)
* [устройства](template-functions-string.md#guid)
* [indexOf](template-functions-string.md#indexof)
* [Последняя](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [length](template-functions-string.md#length)
* [newGuid](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [replace](template-functions-string.md#replace)
* [сразу](template-functions-string.md#skip)
* [биваем](template-functions-string.md#split)
* [startsWith](template-functions-string.md#startswith)
* [string](template-functions-string.md#string)
* [подстроки](template-functions-string.md#substring)
* [нимают](template-functions-string.md#take)
* [toLower](template-functions-string.md#tolower)
* [toUpper](template-functions-string.md#toupper)
* [возмож](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [URI](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>Следующие шаги

* Описание разделов в шаблоне ARM см. в разделе [Создание шаблонов ARM](template-syntax.md) .
* Инструкции по объединению нескольких шаблонов см. в статье [Использование связанных шаблонов в Azure Resource Manager](linked-templates.md).
* Чтобы выполнить итерацию указанного числа раз при создании типа ресурса, см. раздел [Создание нескольких экземпляров ресурсов в Azure Resource Manager](copy-resources.md).
* Сведения о развертывании созданного шаблона см. в разделе [развертывание приложения с помощью шаблонов ARM](deploy-powershell.md) .
