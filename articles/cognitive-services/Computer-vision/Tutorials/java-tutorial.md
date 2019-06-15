---
title: Выполнение операций с изображениями — Java
titlesuffix: Azure Cognitive Services
description: Изучите базовое приложение Java Swing, в котором используется API компьютерного зрения в Azure Cognitive Services. Узнайте, как выполнять распознавание текста, создавать эскизы и работать с визуальными компонентами в изображении.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.author: kefre
ms.custom: seodec18
ms.date: 04/30/2019
ms.openlocfilehash: a22308e0c7ff924205f715692d011a4572b2bdb8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65232628"
---
# <a name="use-computer-vision-features-with-the-rest-api-and-java"></a>Используйте возможности компьютерного зрения с REST API и Java

В этом руководстве демонстрируются функции REST API компьютерного зрения в Azure Cognitive Services.

Исследуйте приложение Java Swing, которое использует REST API компьютерного зрения для оптического распознавания символов (OCR) и создания интеллектуально обрезанных эскизов, а также для обнаружения, классификации, добавления тегов и описания визуальных компонентов изображения, включая лица. В этом примере можно отправить URL-адрес изображения для анализа или обработки. Этот пример с открытым кодом можно использовать как шаблон для создания приложения на языке Java, которое использует REST API компьютерного зрения.

В этом руководстве рассматривается использование API компьютерного зрения для следующих действий.

> [!div class="checklist"]
> * Анализ изображения
> * Определение настоящих или искусственных объектов на изображениях.
> * Определение знаменитостей на изображениях.
> * Создание эскиза качества с изображений.
> * Считывание печатного текста с изображений.
> * Распознавание рукописного текста на изображениях.

Приложение Java Swing уже были записаны, но не имеет функциональных возможностей. В этом руководстве вы добавите код для API компьютерного зрения, чтобы дополнить функциональные возможности приложения.

## <a name="prerequisites"></a>Технические условия

### <a name="platform-requirements"></a>Требования платформы

Это руководство было разработано с помощью NetBeans IDE. Если быть точнее, NetBeans версии **Java SE**, которую можно [загрузить здесь](https://netbeans.org/downloads/index.html).

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Получение подписки API компьютерного зрения и ключа подписки

Прежде чем создавать в примере, необходимо подписаться на API компьютерного зрения компьютера, который является частью Azure Cognitive Services. Сведения о подписках и управлении ключами см. в разделе [Подписки](https://azure.microsoft.com/try/cognitive-services/). В этом руководстве можно использовать как первичный, так и вторичный ключ.

## <a name="acquire-incomplete-tutorial-project"></a>Получить неполные учебного проекта

### <a name="download-the-project"></a>Скачивание проекта

1. Перейдите к репозиторию [руководства по использованию API компьютерного зрения в Cognitive Services Java](https://github.com/Azure-Samples/cognitive-services-java-computer-vision-tutorial).
1. Нажмите кнопку **Копировать или скачать**.
1. Нажмите кнопку **Скачать ZIP** для скачивания ZIP-файла учебного проекта.

Нет необходимости извлекать содержимое ZIP-файла, так как NetBeans импортирует из него проект.

### <a name="import-the-tutorial-project"></a>Импорт учебного проекта

Импортируйте файл **cognitive-services-java-computer-vision-tutorial-master.zip** в NetBeans.

1. В среде разработки NetBeans щелкните **Файл** > **Импорт проекта** > **из ZIP...** . Откроется диалоговое окно **Импорт проектов из ZIP**.
1. В поле **ZIP-файл:** нажмите кнопку **Обзор**, чтобы разместить файл **cognitive-services-java-computer-vision-tutorial-master.zip**, а затем щелкните **Открыть**.
1. В диалоговом окне **Импорт проектов из ZIP** щелкните **Импортировать**.
1. На панели **Проекты**, разверните **ComputerVision** > **Исходные пакеты** >  **&lt;Пакет по умолчанию&gt;** . 
   В некоторых версиях NetBeans используйте **src** вместо **Исходных пакетов** >  **&lt;Пакет по умолчанию&gt;** . В этом случае разверните **src**.
1. Дважды щелкните **MainFrame.java**, чтобы загрузить файл в редакторе NetBeans. Отобразится вкладка **Разработки** файла **MainFrame.java**.
1. Щелкните вкладку **Источник**, чтобы просмотреть исходный код Java.

### <a name="build-and-run-the-tutorial-project"></a>Сборка и запуск учебного проекта

1. Нажмите клавишу **F6**, чтобы создать и запустить учебное приложение.

    В учебном приложении щелкните вкладку, чтобы открыть область для этой функции. Кнопки имеют пустые методы, поэтому они не действуют.

    В нижней части окна размещены два поля **Ключ подписки** и **Подписка региона**. Введите в эти поля действительный ключ подписки и правильный регион для этого ключа. Чтобы получить ключ подписки, см. раздел [Подписки](https://azure.microsoft.com/try/cognitive-services/). Если вы получили ключ подписки с бесплатной пробной версии по этой ссылке, это значит, что регион **westcentralus** по умолчанию является правильным регионом для ключей подписки.

1. Выйдите с учебного приложения.

## <a name="add-tutorial-code-to-the-project"></a>Добавьте код учебника в проект

Приложение Java Swing настроено с шестью вкладками. Каждая вкладка демонстрирует отдельную функцию API Компьютерного зрения (анализ, распознавание текста и т. д). Все шесть разделов руководства являются независимыми, поэтому можно добавить любое их количество. Их можно добавлять в любом порядке.

### <a name="analyze-an-image"></a>Анализ изображения

Функция анализа Компьютерного зрения проверяет изображения, определяя более 2000 распознаваемых объектов, живых существ, пейзажей и действий. После завершения анализа эта функция возвращает объект JSON, описывающий изображение с помощью описательных тегов, анализа цвета, подписей и многого другого.

Чтобы завершить функцию анализа учебного приложения, выполните следующие действия.

#### <a name="add-the-event-handler-code-for-the-analyze-button"></a>Добавьте код обработчика событий для кнопки "Анализ"

Метод обработчика событий **analyzeImageButtonActionPerformed** очищает форму, отображает изображение, указанное в URL-адресе, а затем вызывает метод **AnalyzeImage** для анализа этого изображения. Когда возвращается **AnalyzeImage**, этот метод отображает отформатированный ответ JSON в текстовой области **Response** (Ответ), извлекает первый заголовок из **JSONObject** и отображает подпись и уровень надежности, подтверждающий что подпись правильная.

Скопируйте приведенный ниже код и вставьте его в метод **analyzeImageButtonActionPerformed**.

> [!NOTE]
> NetBeans не позволит вставить в строку определение метода (```private void```) или закрыть его фигурные скобки. Чтобы скопировать код, скопируйте строки между определением метода и закрывающей фигурной скобкой и вставьте их поверх содержимого метода.

```java
    private void analyzeImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL analyzeImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        analyzeImage.setIcon(new ImageIcon());
        analyzeCaptionLabel.setText("");
        analyzeResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            analyzeImageUrl = new URL(analyzeImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(analyzeImageUrl);
            scaleAndShowImage(bImage, analyzeImage);
        } catch(IOException e) {
            analyzeResponseTextArea.setText("Error loading Analyze image: " + e.getMessage());
            return;
        }
        
        // Analyze the image.
        JSONObject jsonObj = AnalyzeImage(analyzeImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        analyzeResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("description") && jsonObj.getJSONObject("description").has("captions")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("description").getJSONArray("captions").getJSONObject(0);
            
            if (jsonCaption.has("text") && jsonCaption.has("confidence")) {
                
                analyzeCaptionLabel.setText("Caption: " + jsonCaption.getString("text") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Добавление программы-оболочки для вызова REST API

Метод **AnalyzeImage** создает программу-оболочку для вызова REST API для анализа изображения. Этот метод возвращает **JSONObject**, описывающий образ, или **null**, если произошла ошибка.

Скопируйте и вставьте метод **AnalyzeImage** сразу же под методом **analyzeImageButtonActionPerformed**.

```java
    /**
     * Encapsulates the Microsoft Cognitive Services REST API call to analyze an image.
     * @param imageUrl: The string URL of the image to analyze.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject AnalyzeImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call for Analyze Image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseAnalyze;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color,Adult");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
 ```

#### <a name="run-the-analyze-function"></a>Запустите функцию анализ

Нажмите клавишу **F6** для запуска приложения. Поместите ключ подписки в поле **Subscription Key** (Ключ подписки) и убедитесь, что вы используете правильный регион в поле **Subscription Region** (Регион подписки). Введите URL-адрес изображения, которое нужно проанализировать, а затем нажмите кнопку **Analyze Image** (Анализировать изображение), чтобы проанализировать изображение и просмотреть результат.

### <a name="recognize-a-landmark"></a>Распознавание ориентира

Функция распознавания ориентиров в API компьютерного зрения позволяет анализировать изображения для поиска природных и искусственных ориентиров, например гор или известных зданий. После завершения анализа функция распознавания ориентиров возвращает объект JSON, в котором определены ориентиры, найденные в изображении.

Чтобы завершить функцию распознавания ориентиров учебного приложения, выполните следующие действия.

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Добавление кода обработчика событий для кнопки формы

Метод обработчика событий **landmarkImageButtonActionPerformed** очищает форму, отображает изображение, указанное в URL-адресе, а затем вызывает метод **LandmarkImage** для анализа этого изображения. Когда возвращается **LandmarkImage**, метод отображает отформатированный ответ JSON в текстовой области **Response** (Ответ), затем извлекает первое имя ориентира из **JSONObject** и отображает его в окне наряду с уровнем надежности, который был идентифицирован верно.

Скопируйте приведенный ниже код и вставьте его в метод **landmarkImageButtonActionPerformed**.

> [!NOTE]
> NetBeans не позволит вставить в строку определение метода (```private void```) или закрыть его фигурные скобки. Чтобы скопировать код, скопируйте строки между определением метода и закрывающей фигурной скобкой и вставьте их поверх содержимого метода.

```java
    private void landmarkImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL landmarkImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        landmarkImage.setIcon(new ImageIcon());
        landmarkCaptionLabel.setText("");
        landmarkResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            landmarkImageUrl = new URL(landmarkImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(landmarkImageUrl);
            scaleAndShowImage(bImage, landmarkImage);
        } catch(IOException e) {
            landmarkResponseTextArea.setText("Error loading Landmark image: " + e.getMessage());
            return;
        }
        
        // Identify the landmark in the image.
        JSONObject jsonObj = LandmarkImage(landmarkImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        landmarkResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("landmarks")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("landmarks").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                landmarkCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Добавление программы-оболочки для вызова REST API

Метод **LandmarkImage** создает программу-оболочку для вызова REST API для анализа изображения. Этот метод возвращает **JSONObject**, описывающий ориентиры, найденные на изображении, или **null**, если произошла ошибка.

Скопируйте и вставьте метод **LandmarkImage** сразу же под методом **landmarkImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify a landmark in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject LandmarkImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify a Landmark in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseLandmark;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-landmark-function"></a>Запустите функцию ориентиров

Нажмите клавишу **F6** для запуска приложения. Поместите ключ подписки в поле **Subscription Key** (Ключ подписки) и убедитесь, что вы используете правильный регион в поле **Subscription Region** (Регион подписки). Щелкните вкладку **Landmark** (Ориентир), введите URL-адрес изображения ориентира, а затем нажмите кнопку **Analyze Image** (Анализировать изображение), чтобы проанализировать изображение и просмотреть результат.

### <a name="recognize-celebrities"></a>Распознавание знаменитостей

Функция распознавания знаменитостей API компьютерного зрения анализирует изображения для выявления знаменитых людей. После завершения анализа функция распознавания знаменитостей возвращает объект JSON, в котором определены знаменитости, найденные в изображении.

Чтобы завершить функцию распознавания знаменитостей учебного приложения, выполните следующие действия.

#### <a name="add-the-event-handler-code-for-the-celebrities-button"></a>Добавьте код обработчика событий для кнопки знаменитостей

Метод обработчика событий **celebritiesImageButtonActionPerformed** очищает форму, отображает изображение, указанное в URL-адресе, а затем вызывает метод **CelebritiesImage** для анализа этого изображения. Когда возвращается **CelebritiesImage**, метод отображает отформатированный ответ JSON в текстовой области **Response** (Ответ), затем извлекает первое имя знаменитости из **JSONObject** и отображает его имя в окне наряду с уровнем надежности, что свидетельствует о правильной идентификации знаменитости.

Скопируйте приведенный ниже код и вставьте его в метод **celebritiesImageButtonActionPerformed**.

> [!NOTE]
> NetBeans не позволит вставить в строку определение метода (```private void```) или закрыть его фигурные скобки. Чтобы скопировать код, скопируйте строки между определением метода и закрывающей фигурной скобкой и вставьте их поверх содержимого метода.

```java
    private void celebritiesImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL celebritiesImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        celebritiesImage.setIcon(new ImageIcon());
        celebritiesCaptionLabel.setText("");
        celebritiesResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            celebritiesImageUrl = new URL(celebritiesImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(celebritiesImageUrl);
            scaleAndShowImage(bImage, celebritiesImage);
        } catch(IOException e) {
            celebritiesResponseTextArea.setText("Error loading Celebrity image: " + e.getMessage());
            return;
        }
        
        // Identify the celebrities in the image.
        JSONObject jsonObj = CelebritiesImage(celebritiesImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        celebritiesResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("celebrities")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("celebrities").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                celebritiesCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Добавление программы-оболочки для вызова REST API

Метод **CelebritiesImage** создает программу-оболочку для вызова REST API для анализа изображения. Этот метод возвращает **JSONObject**, описывающий знаменитостей, найденных на изображении, или **null**, если произошла ошибка.

Скопируйте и вставьте метод **CelebritiesImage** сразу же под методом **celebritiesImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify celebrities in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject CelebritiesImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseCelebrities;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-celebrities-function"></a>Запуск функции знаменитостей

Нажмите клавишу **F6** для запуска приложения. Поместите ключ подписки в поле **Subscription Key** (Ключ подписки) и убедитесь, что вы используете правильный регион в поле **Subscription Region** (Регион подписки). Щелкните вкладку **Celebrities** (Знаменитости), введите URL-адрес изображения знаменитости, которое нужно проанализировать, а затем нажмите кнопку **Analyze Image** (Анализировать изображение), чтобы проанализировать изображение и просмотреть результат.

### <a name="intelligently-generate-a-thumbnail"></a>Интеллектуальное создание эскиза

Функция создания эскизов API компьютерного зрения создает эскиз изображения. С помощью функции **интеллектуальной обрезки** компонент создания эскизов определит интересующую область в изображении и отцентрирует эскиз по данной области, чтобы сделать его более эстетичным.

Чтобы завершить функцию создания эскизов учебного приложения, выполните следующие действия.

#### <a name="add-the-event-handler-code-for-the-thumbnail-button"></a>Добавьте код обработчика событий для кнопки эскиза

Метод обработчика событий **thumbnailImageButtonActionPerformed** очищает форму, отображает изображение, указанное в URL-адресе, а затем вызывает метод **getThumbnailImage** для создания эскиза. Когда возвращается **getThumbnailImage**, метод отображает созданный эскиз.

Скопируйте приведенный ниже код и вставьте его в метод **thumbnailImageButtonActionPerformed**.

> [!NOTE]
> NetBeans не позволит вставить в строку определение метода (```private void```) или закрыть его фигурные скобки. Чтобы скопировать код, скопируйте строки между определением метода и закрывающей фигурной скобкой и вставьте их поверх содержимого метода.

```java
    private void thumbnailImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL thumbnailImageUrl;
        JSONObject jsonError[] = new JSONObject[1];
        
        // Clear out the previous image, response, and thumbnail, if any.
        thumbnailSourceImage.setIcon(new ImageIcon());
        thumbnailResponseTextArea.setText("");
        thumbnailImage.setIcon(new ImageIcon());

        // Display the image specified in the text box.
        try {
            thumbnailImageUrl = new URL(thumbnailImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(thumbnailImageUrl);
            scaleAndShowImage(bImage, thumbnailSourceImage);
        } catch(IOException e) {
            thumbnailResponseTextArea.setText("Error loading image to thumbnail: " + e.getMessage());
            return;
        }
        
        // Get the thumbnail for the image.
        BufferedImage thumbnail = getThumbnailImage(thumbnailImageUrl.toString(), jsonError);
        
        // A non-null value indicates error.
        if (jsonError[0] != null) {
            // Format and display the JSON error.
            thumbnailResponseTextArea.setText(jsonError[0].toString(2));
            return;
        }
        
        // Display the thumbnail.
        if (thumbnail != null) {
            scaleAndShowImage(thumbnail, thumbnailImage);
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Добавление программы-оболочки для вызова REST API

Метод **getThumbnailImage** создает программу-оболочку для вызова REST API для анализа изображения. Этот метод возвращает **BufferedImage**, содержащий эскиз, или **null**, если произошла ошибка. Сообщение об ошибке будет возвращаться в первом элементе массива строк **jsonError**.

Скопируйте и вставьте следующий метод **getThumbnailImage** сразу же под методом **thumbnailImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to create a thumbnail for an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A BufferedImage containing the thumbnail, or null if a runtime error occurs. In the case
     * of an error, the error message will be returned in the first element of the jsonError string array.
     */
    private BufferedImage getThumbnailImage(String imageUrl, JSONObject[] jsonError) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseThumbnail;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity requestEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(requestEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200)
            {
                // Return the thumbnail.
                return ImageIO.read(entity.getContent());
            }
            else
            {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                jsonError[0] = new JSONObject(jsonString);
                return null;
            }
        }
        catch (Exception e)
        {
            String errorMessage = e.getMessage();
            System.out.println(errorMessage);
            jsonError[0] = new JSONObject(errorMessage);
            return null;
        }
    }
```

#### <a name="run-the-thumbnail-function"></a>Запустите функцию эскиза

Нажмите клавишу **F6** для запуска приложения. Поместите ключ подписки в поле **Subscription Key** (Ключ подписки) и убедитесь, что вы используете правильный регион в поле **Subscription Region** (Регион подписки). Щелкните вкладку **Thumbnail** (Ориентир), введите URL-адрес изображения ориентира, а затем нажмите кнопку **Generate Thumbnail** (Создать эскиз), чтобы проанализировать изображение и просмотреть результат.

### <a name="read-printed-text-ocr"></a>Распознавание печатного текста (OCR)

Функция оптического распознавания символов (OCR) API компьютерного зрения анализирует изображение печатного текста. После завершения анализа функция OCR возвращает объект JSON, содержащий текст и расположение текста в изображении.

Чтобы завершить функцию OCR учебного приложения, выполните следующие действия.

#### <a name="add-the-event-handler-code-for-the-ocr-button"></a>Добавьте код обработчика событий для кнопки OCR

Метод обработчика событий **ocrImageButtonActionPerformed** очищает форму, отображает изображение, указанное в URL-адресе, а затем вызывает метод **OcrImage** для анализа этого изображения. Когда возвращается **OcrImage**, метод отображает обнаруженный текст в формате JSON в текстовой области **Response** (Ответ).

Скопируйте приведенный ниже код и вставьте его в метод **ocrImageButtonActionPerformed**.

> [!NOTE]
> NetBeans не позволит вставить в строку определение метода (```private void```) или закрыть его фигурные скобки. Чтобы скопировать код, скопируйте строки между определением метода и закрывающей фигурной скобкой и вставьте их поверх содержимого метода.

```java
    private void ocrImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL ocrImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        ocrImage.setIcon(new ImageIcon());
        ocrResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            ocrImageUrl = new URL(ocrImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(ocrImageUrl);
            scaleAndShowImage(bImage, ocrImage);
        } catch(IOException e) {
            ocrResponseTextArea.setText("Error loading OCR image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = OcrImage(ocrImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        ocrResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Добавление программы-оболочки для вызова REST API

Метод **OcrImage** создает программу-оболочку для вызова REST API для анализа изображения. Этот метод возвращает **JSONObject** данных JSON, возвращаемых из вызова, или **null**, если произошла ошибка.

Скопируйте и вставьте метод **OcrImage** сразу же под методом **ocrImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject OcrImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseOcr;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("language", "unk");
            uriBuilder.setParameter("detectOrientation ", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-ocr-function"></a>Функция OCR

Нажмите клавишу **F6** для запуска приложения. Поместите ключ подписки в поле **Subscription Key** (Ключ подписки) и убедитесь, что вы используете правильный регион в поле **Subscription Region** (Регион подписки). Щелкните вкладку **OCR** (Ориентир), введите URL-адрес изображения печатного текста, а затем нажмите кнопку **Read Image** (Считать изображение), чтобы проанализировать изображение и просмотреть результат.

### <a name="read-handwritten-text-handwriting-recognition"></a>Распознавание рукописного текста

Функция распознавания рукописного текста API компьютерного зрения анализирует изображение рукописного текста. После завершения анализа функция распознавания рукописного текста возвращает объект JSON, содержащий текст и расположение текста в изображении.

Чтобы завершить функцию распознавания рукописного текста учебного приложения, выполните следующие действия.

#### <a name="add-the-event-handler-code-for-the-handwriting-button"></a>Добавьте код обработчика событий для кнопки рукописного ввода

Метод обработчика событий **handwritingImageButtonActionPerformed** очищает форму, отображает изображение, указанное в URL-адресе, а затем вызывает метод **HandwritingImage** для анализа этого изображения. Когда возвращается **HandwritingImage**, метод отображает обнаруженный текст в формате JSON в текстовой области **Response** (Ответ).

Скопируйте приведенный ниже код и вставьте его в метод **handwritingImageButtonActionPerformed**.

> [!NOTE]
> NetBeans не позволит вставить в строку определение метода (```private void```) или закрыть его фигурные скобки. Чтобы скопировать код, скопируйте строки между определением метода и закрывающей фигурной скобкой и вставьте их поверх содержимого метода.

```java
    private void handwritingImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL handwritingImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        handwritingImage.setIcon(new ImageIcon());
        handwritingResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            handwritingImageUrl = new URL(handwritingImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(handwritingImageUrl);
            scaleAndShowImage(bImage, handwritingImage);
        } catch(IOException e) {
            handwritingResponseTextArea.setText("Error loading Handwriting image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = HandwritingImage(handwritingImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        handwritingResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Добавление программы-оболочки для вызова REST API

Метод **HandwritingImage** создает программу-оболочку для двух вызовов REST API, необходимых для анализа изображения. Так как распознавание рукописного текста требует много времени, используется обработка в два этапа. Первый вызов отправляет изображение для обработки. Второй вызов извлекает обнаруженный текст после завершения обработки.

После извлечения текста метод **HandwritingImage** возвращает **JSONObject**, описывающий текст и его расположение, или **null**, если произошла ошибка.

Скопируйте и вставьте метод **HandwritingImage** сразу же под методом **handwritingImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read handwritten text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject HandwritingImage(String imageUrl) {
        try (CloseableHttpClient textClient = HttpClientBuilder.create().build();
             CloseableHttpClient resultClient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseHandwriting;
            URIBuilder uriBuilder = new URIBuilder(uriString);
            
            // Request parameters.
            uriBuilder.setParameter("handwriting", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response.
            HttpResponse textResponse = textClient.execute(request);
            
            // Check for success.
            if (textResponse.getStatusLine().getStatusCode() != 202) {
                // An error occurred. Return the JSON error message.
                HttpEntity entity = textResponse.getEntity();
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            }
            
            String operationLocation = null;

            // The 'Operation-Location' in the response contains the URI to retrieve the recognized text.
            Header[] responseHeaders = textResponse.getAllHeaders();
            for(Header header : responseHeaders) {
                if(header.getName().equals("Operation-Location"))
                {
                    // This string is the URI where you can get the text recognition operation result.
                    operationLocation = header.getValue();
                    break;
                }
            }
            
            // NOTE: The response may not be immediately available. Handwriting recognition is an
            // async operation that can take a variable amount of time depending on the length
            // of the text you want to recognize. You may need to wait or retry this operation.
            //
            // This example checks once per second for ten seconds.
            
            JSONObject responseObj = null;
            int i = 0;
            do {
                // Wait one second.
                Thread.sleep(1000);
                
                // Check to see if the operation completed.
                HttpGet resultRequest = new HttpGet(operationLocation);
                resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());
                HttpResponse resultResponse = resultClient.execute(resultRequest);
                HttpEntity responseEntity = resultResponse.getEntity();
                if (responseEntity != null)
                {
                    // Get the JSON response.
                    String jsonString = EntityUtils.toString(responseEntity);
                    responseObj = new JSONObject(jsonString);
                }
            }
            while (i < 10 && responseObj != null &&
                    !responseObj.getString("status").equalsIgnoreCase("Succeeded"));
            
            // If the operation completed, return the JSON object.
            if (responseObj != null) {
                return responseObj;
            } else {
                // Return null for timeout error.
                System.out.println("Timeout error.");
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-handwriting-function"></a>Запуск функций рукописного ввода

Нажмите клавишу **F6** для запуска приложения. Поместите ключ подписки в поле **Subscription Key** (Ключ подписки) и убедитесь, что вы используете правильный регион в поле **Subscription Region** (Регион подписки). Щелкните вкладку **Read Handwritten Text** (Распознавание рукописного текста), введите URL-адрес изображения рукописного текста, а затем нажмите кнопку **Read Image** (Считать изображение), чтобы проанализировать изображение и просмотреть результат.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы использовали REST API компьютерного зрения с помощью Java для тестирования многих возможностей анализа доступных образа. Далее см. в справочной документации, чтобы узнать больше об API, участвующих.

- [REST API компьютерного зрения](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)
