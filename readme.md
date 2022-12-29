# Домашнее задание "Получаем картинку от NASA, создаём бота и затем делаем его автономным"

------

### Что нужно сделать
1. Получить ключ для API NASA по адресу: https://api.nasa.gov/ <br>
2. Сделать запрос из кода: https://api.nasa.gov/planetary/apod?api_key=ВАШ_КЛЮЧ <br>
3. Создать класс ответа и разобрать json-ответ с помощью Jackson или Gson <br>
4. Найти поле url в ответе и скачать массив byte, который сохранить в файл <br>
5. Имя файла должно быть взято из части url

### Описание 

Нужно воспользоваться публичным API NASA и скачать ежедневно выгружаемое им изображение. Несмотря на то, что API публичный, доступ к нему предоставляется по ключу, который достаточно просто получить по адресу: https://api.nasa.gov/. Перейдя по ссылке, заполняем личными данными поля:
* First Name
* Last Name
* Email

В ответ (а так же на почтовый адрес) будет выслан ключ. С этим ключом нужно делать запросы к API.

Итак, чтобы получить ссылку на картинку или другой контент, нужно:

1. Сделать запрос по адресу: https://api.nasa.gov/planetary/apod?api_key=ВАШ_КЛЮЧ
2. Разобрать полученный ответ
3. В ответе найти поле `url` - оно содержит адрес на изображение, которое нужно скачать и сохранить локально (на своем компьютере), имя сохраняемого файла нужно взять из части url (из примера ниже DSC1028_PetersNEOWISEAuroralSpike_800.jpg)
4. Проверить что сохраненный файл открывается.

Пример ответа сервиса NASA

```cs{
{
  "copyright": "Bill Peters",
  "date": "2020-07-17",
  "explanation": "After local midnight on July 14 comet NEOWISE was still above the horizon for Goldenrod, Alberta, Canada, just north of Calgary, planet Earth. In this snapshot it makes for an awesome night with dancing displays of the northern lights. The long-tailed comet and auroral displays are beautiful apparitions in the north these days. Both show the influence of spaceweather and the wind from the Sun. Skygazers have widely welcomed the visitor from the Oort cloud, though C/2020 F3 (NEOWISE) is in an orbit that is now taking it out of the inner Solar System.  Comet NEOWISE Images: July 16 | July 15 | July 14 | July 13 | July 12 | July 11 | July 10 & earlier",
  "hdurl": "https://apod.nasa.gov/apod/image/2007/DSC1028_PetersNEOWISEAuroralSpike.jpg",
  "media_type": "image",
  "service_version": "v1",
  "title": "NEOWISE of the North",
  "url": "https://apod.nasa.gov/apod/image/2007/DSC1028_PetersNEOWISEAuroralSpike_800.jpg"
}
```

### Реализация
1. Создайте проект `maven` или `gradle` и добавьте в pom.xml или gradle.build библиотеку apache httpclient

Пример:

```cs{
<dependency>
   <groupId>org.apache.httpcomponents</groupId>
   <artifactId>httpclient</artifactId>
   <version>4.5.12</version>
</dependency>
```

2. Создайте метод в который добавьте и настройте класс `CloseableHttpClient` например с помощью builder

```cs{
CloseableHttpClient httpClient = HttpClientBuilder.create()
    .setDefaultRequestConfig(RequestConfig.custom()
        .setConnectTimeout(5000)    // максимальное время ожидание подключения к серверу
        .setSocketTimeout(30000)    // максимальное время ожидания получения данных
        .setRedirectsEnabled(false) // возможность следовать редиректу в ответе
        .build())
    .build();
```

3. Добавьте объект запроса HttpGet request = new HttpGet("https://api.nasa.gov/planetary/apod?api_key=ВАШ_КЛЮЧ") и вызовите удаленный сервис `CloseableHttpResponse response = httpClient.execute(request)`;<br>
4. Добавьте в pom.xml или gradle.build библиотеку для работы с json

Пример:

```cs{
<dependency>
   <groupId>com.fasterxml.jackson.core</groupId>
   <artifactId>jackson-databind</artifactId>
   <version>2.11.1</version>
</dependency>
```

5. Создайте класс, в который будем преобразовывать json ответ от сервера;<br>
6. Преобразуйте json в java-объект;<br>
7. В java-объекте найдите поле url и сделайте с ним еще один http-запрос с помощью уже созданного httpClient;<br>
8. Сохраните тело ответа в файл с именем части url;<br>
9. Проверьте, что файл скачивается и открывается;<br>
10. Готово!


1. Зарегистрировать бота в приложении Telegram
2. Получить ключ для нашего бота
3. Получить наш chatID
4. Подключить зависимость Telegram API к нашему проекту
5. Написать бота


### Создаём бота
1. В классе MyTelegramBot описан бот. Прежде чем начинать разработку, бота необходимо зарегистрировать и получить его уникальный id, являющийся одновременно и токеном. Для этого в Telegram существует специальный бот — @BotFather.

Пишем ему /start и получаем список всех его команд.
Первая и главная — /newbot — отправляем ему и бот просит придумать имя нашему новому боту. Единственное ограничение на имя — оно должно оканчиваться на «bot». В случае успеха BotFather возвращает токен бота и ссылку для быстрого добавления бота в контакты, иначе придется поломать голову над именем.

    public static final String BOT_TOKEN = "ВАШ ТОККЕН";

    public static final String BOT_USERNAME = "ИМЯ БОТА ТЕЛЕГРАММ";

В эти поля нужно внести полученные данные.
После чего наш код полностью имеет завершенный вид и может быть испольщзоваться по своему назначению.
2. Дальнейшими действия будет создать на основе нашего кода jar файл, который в свою очередь будет помещён на облачный сервис, на котором будет установлена виртуальная машина JDK 11 версии и пущен данный файл.
3. В Идеи создаём jar файл:
   Файл> Структура проекта.
   Выберите вкладку «Артефакты».
   Нажмите зеленую кнопку с плюсом в верхней части окна.
   В раскрывающемся меню «Добавить» выберите JAR. Выберите «Из модулей с зависимостями».
   Выберите основной класс.
   Кнопка должна быть выбрана «извлечь в целевой JAR». Нажмите ОК.
   Установите флажок "Создавать на основе make".
   Нажмите «Применить» и «ОК».
   В главном меню выберите раскрывающееся меню сборки.
   Выберите параметр сборки артефактов.

### Делаем его автономным
1. Зарегистрироваться на Yandex Cloud
2. Для доступа нам понадобится ssh ключ (Я работаю на Windows 10 - описание будет для этой ОС)
 2.1. В коммандной строке: C:\Users\ИМЯ>ssh-keygen
   Далее нажимаете Enter - и папке по умолчанию вы получеете ключ, для публиного использования вам понадобится id_rsa.pub
   Из него нужно будет скопировать и вставить в окне при создании ВМ описанной ниже, так же придумать имя вашей машине которой потом нам понадобится.
3. Создать виртуальную машину Yandex Cloud (выбираем при регистрации ознакомительный период, чтобы не платить деньги, в настройках выбираем самые минимизированные настрйки сервера под наши задачи, размер HDD, процессор, ОЗУ и т.д.)
4. Подключаемся к виртуальной машинe через ключ доступа ssh.
5. Устанавливаем на виртуальную машину JDK 11.
6. Останавливает подключение.
5. Переносим jar на виртуальную машину.
6. Проверяем наличие jar в облаке.
6. Запускает его.
7. Готово, наш телеграмм бот стал автономным.
8. Если для использования он вам не нужен, его можно отключить.
------


