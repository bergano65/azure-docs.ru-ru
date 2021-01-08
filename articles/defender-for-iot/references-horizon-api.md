---
title: API горизонта
description: В этом руководством описываются часто используемые методы горизонта.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/7/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 6d2e3fccd6a61fe129050faa29cb7bb77674ccfe
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97976919"
---
# <a name="horizon-api"></a>API горизонта 

В этом руководством описываются часто используемые методы горизонта.

### <a name="getting-more-information"></a>Получение дополнительных сведений

Дополнительные сведения о работе с горизонтом и платформой Циберкс см. в следующих источниках:

- Чтобы получить доступ к пакету SDK для среды разработки горизонта (node), обратитесь к своему представителя Циберкс.
- Для получения сведений о поддержке и устранении неполадок обратитесь к <support@cyberx-labs.com> .
- Чтобы получить доступ к руководству пользователя Циберкс из консоли Циберкс, выберите :::image type="icon" source="media/references-horizon-api/profile-icon.png"::: , а затем щелкните **загрузить пользовательское Guide**.

## `horizon::protocol::BaseParser`

Аннотация для всех подключаемых модулей. Это состоит из двух методов:

- Для обработки фильтров подключаемых модулей, определенных выше. Таким образом, горизонту будет известно, как взаимодействовать с анализатором
- Для обработки фактических данных.

## `std::shared_ptr<horizon::protocol::BaseParser> create_parser()`

Первая функция, вызываемая для подключаемого модуля, создает экземпляр средства синтаксического анализа для его распознавания и регистрации.

### <a name="parameters"></a>Параметры 

Отсутствуют

### <a name="return-value"></a>Возвращаемое значение

shared_ptr к экземпляру средства синтаксического анализа.

## `std::vector<uint64_t> horizon::protocol::BaseParser::processDissectAs(const std::map<std::string, std::vector<std::string>> &) const`

Эта функция будет вызываться для каждого подключаемого модуля, зарегистрированного выше. 

В большинстве случаев это будет пустым. Вызовите исключение для горизонта, чтобы получить сведения о том, что произошло неверно.

### <a name="parameters"></a>Параметры 

- Схема, содержащая структуру dissect_as, как определено в config.jsв другом подключаемом модуле, который требуется зарегистрировать.

### <a name="return-value"></a>Возвращаемое значение 

Массив uint64_t, который обрабатывается регистрацией в виде uint64_t. Это означает, что на карте имеется список портов, значения которых будут uin64_t.

## `horizon::protocol::ParserResult horizon::protocol::BaseParser::processLayer(horizon::protocol::management::IProcessingUtils &,horizon::general::IDataBuffer &)`

Функция Main. В частности, логику подключаемого модуля, каждый раз, когда новый пакет достигает средства синтаксического анализа. Эта функция будет вызвана, все, что связано с обработкой пакетов, должно быть сделано здесь.

### <a name="considerations"></a>Рекомендации

Подключаемый модуль должен быть потокобезопасным, так как эта функция может быть вызвана из разных потоков. Хороший подход — определить все в стеке.

### <a name="parameters"></a>Параметры

- Модуль управления пакетом SDK, отвечающий за хранение данных и создание связанных объектов SDK, таких как Илайер, поля и т. д.
- Вспомогательный объект для чтения данных необработанного пакета. Для него уже задан порядок байтов, определенный в config.js.

### <a name="return-value"></a>Возвращаемое значение 

Результат обработки. Это может быть либо Success, либо неправильно сформированный/работоспособности.

## `horizon::protocol::SanityFailureResult: public horizon::protocol::ParserResult`

Помечает обработку как сбой самому, то есть пакет не распознается текущим протоколом, и горизонт должен передать его другому средству синтаксического анализа, если они зарегистрированы в том же фильтре.

## `horizon::protocol::SanityFailureResult::SanityFailureResult(uint64_t)`

Конструктор

### <a name="parameters"></a>Параметры 

- Определяет код ошибки, используемый горизонтом для ведения журнала, как определено в config.json.

## `horizon::protocol::MalformedResult: public horizon::protocol::ParserResult`

Неправильно сформированный результат. указан, что мы уже распознал пакет как наш протокол, но при некоторых проверках произошла ошибка (зарезервированные биты включены, отсутствует некоторое поле и т. д.)

## `horizon::protocol::MalformedResult::MalformedResult(uint64_t)`

Конструктор

### <a name="parameters"></a>Параметры  

- Код ошибки, как определено в config.json.

## `horizon::protocol::SuccessResult: public horizon::protocol::ParserResult`

Уведомляет горизонт успешной обработки. При успешном выполнении пакет принят; данные принадлежат нам, и все данные были извлечены.

## `horizon::protocol::SuccessResult()`

Конструктор. Создание базового успешного результата. Это означает, что мы не понимаем направление или другие метаданные, касающиеся пакета.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection)`

Конструктор

### <a name="parameters"></a>Параметры 

- Направление пакета, если оно определено. Значения могут быть ЗАПРОСом, ОТВЕТом

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection, const std::vector<uint64_t> &)`

Конструктор

### <a name="parameters"></a>Параметры

- Направление пакета, если он определен, может быть ЗАПРОСом, ОТВЕТом
- Предупреждения. Эти события не будут выполняться, но будут уведомлены горизонт.

## `horizon::protocol::SuccessResult(const std::vector<uint64_t> &)`

Конструктор

### <a name="parameters"></a>Параметры 

-  Предупреждения. Эти события не будут выполняться, но будут уведомлены горизонт.

## `HorizonID HORIZON_FIELD(const std::string_view &)`

Преобразует ссылку на строки в имя поля (например, function_code) в Хоризонид

### <a name="parameters"></a>Параметры 

- Строка для преобразования

### <a name="return-value"></a>Возвращаемое значение

- Хоризонид, созданный из строки.

## `horizon::protocol::ILayer &horizon::protocol::management::IProcessingUtils::createNewLayer()`

Создает новый слой, чтобы он знал, что подключаемый модуль хочет сохранить некоторые данные. Это базовая единица хранения, которую следует использовать.

### <a name="return-value"></a>Возвращаемое значение

Ссылка на созданный слой, позволяющая добавлять в него данные.

## `horizon::protocol::management::IFieldManagement &horizon::protocol::management::IProcessingUtils::getFieldsManager()`

Возвращает объект управления полями, отвечающий за создание полей в различных объектах, например в Илайер

### <a name="return-value"></a>Возвращаемое значение

Ссылка на руководителя

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, uint64_t)`

Создает новое числовое поле 64 бит на слое с запрошенным ИДЕНТИФИКАТОРом.

### <a name="parameters"></a>Параметры 

- Созданный ранее слой
- Хоризонид, созданная макросом HORIZON_FIELD
- Необработанное значение, которое необходимо сохранить

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::string)`

Создает новое строковое поле в слое с запрошенным ИДЕНТИФИКАТОРом. Память будет перемещена, поэтому будьте внимательны. Вы не сможете использовать это значение еще раз.

### <a name="parameters"></a>Параметры  

- Созданный ранее слой
- Хоризонид, созданная макросом HORIZON_FIELD
- Необработанное значение, которое необходимо сохранить

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::vector<char> &)`

Создает новое необработанное значение (массив байтов) в слое с запрошенным ИДЕНТИФИКАТОРом. Память будет перемещена, поэтому будьте осторожны, вы не сможете повторно использовать это значение.

### <a name="parameters"></a>Параметры

- Созданный ранее слой
- Хоризонид, созданная макросом HORIZON_FIELD
- Необработанное значение, которое необходимо сохранить

## `horizon::protocol::IFieldValueArray &horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, horizon::protocol::FieldValueType)`

Создает поле массива значений (массива) на слое указанного типа с запрошенным ИДЕНТИФИКАТОРом.

### <a name="parameters"></a>Параметры

- Созданный ранее слой
- Хоризонид, созданная макросом HORIZON_FIELD
- Тип значений, которые будут храниться внутри массива

### <a name="return-value"></a>Возвращаемое значение

Ссылка на массив, к которому следует добавить значения

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, uint64_t)`

Добавляет новое целочисленное значение в созданный ранее массив

### <a name="parameters"></a>Параметры

- Созданный ранее массив
- Необработанное значение, сохраняемое в массиве

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::string)`

Добавляет новое строковое значение в созданный ранее массив. Память будет перемещена, поэтому будьте осторожны, вы не сможете повторно использовать это значение.

### <a name="parameters"></a>Параметры

- Созданный ранее массив
- Необработанное значение, сохраняемое в массиве

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::vector<char> &)`

Добавляет новое необработанное значение в массив, созданный ранее. Память будет перемещена, поэтому будьте осторожны, вы не сможете повторно использовать это значение.

### <a name="parameters"></a>Параметры

- Созданный ранее массив
- Необработанное значение, сохраняемое в массиве

## `bool horizon::general::IDataBuffer::validateRemainingSize(size_t)`

Проверяет, что буфер содержит не менее X байт.

### <a name="parameters"></a>Параметры

Должно существовать число байтов 

### <a name="return-value"></a>Возвращаемое значение

Значение true, если буфер содержит не менее X байт. В противном случае — значение false.

## `uint8_t horizon::general::IDataBuffer::readUInt8()`

Считывает значение Uint8 (1 байт) из буфера в соответствии с порядком байтов.

### <a name="return-value"></a>Возвращаемое значение

Значение, считанное из буфера.

## `uint16_t horizon::general::IDataBuffer::readUInt16()`

Считывает значение UInt16 (2 байта) из буфера в соответствии с порядком байтов.

### <a name="return-value"></a>Возвращаемое значение

Значение, считанное из буфера.

## `uint32_t horizon::general::IDataBuffer::readUInt32()`

Считывает значение UInt32 (4 байта) из буфера в соответствии с порядком байтов.

### <a name="return-value"></a>Возвращаемое значение

Значение, считанное из буфера.

## `uint64_t horizon::general::IDataBuffer::readUInt64()`

Считывает значение UInt64 (8 байт) из буфера в соответствии с порядком байтов.

### <a name="return-value"></a>Возвращаемое значение

Значение, считанное из буфера.

## `void horizon::general::IDataBuffer::readIntoRawData(void *, size_t)`

Операции чтения в предварительно выделенной памяти указанного размера приводят к копированию данных в область памяти.

### <a name="parameters"></a>Параметры 

- Область памяти, в которую копируются данные
- Размер области памяти, этот параметр также определяет, сколько байтов будет скопировано

## `std::string_view horizon::general::IDataBuffer::readString(size_t)`

Считывает строку из буфера

### <a name="parameters"></a>Параметры 

- Число байтов, которое необходимо считать.

### <a name="return-value"></a>Возвращаемое значение

Ссылка на область памяти строки.

## `size_t horizon::general::IDataBuffer::getRemainingData()`

Указывает, сколько байтов осталось в буфере.

### <a name="return-value"></a>Возвращаемое значение

Оставшийся размер буфера.

## `void horizon::general::IDataBuffer::skip(size_t)`

Пропускает X байт в буфере.

### <a name="parameters"></a>Параметры

- Число пропускаемых байтов.
