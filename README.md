# Требования
* PHP >= 8.0
* MySQL 8.0
* PHP Fileinfo extension

И одна из следующих библиотек для работы с изображениями
* GD Library (>=2.0) … или …
* Imagick PHP extension (>=6.5.7)

По умолчанию используется библиотека GD. Можно менять через переменную `PHP_IMAGE_LIBRARY`. Поддерживаются значения `gd` и `imagick`

# Установка
```
composer install --no-dev
php artisan migrate
```
Если необходимо главная страница
```
npm install
npm run prod
```
Единственный роут приложения будет работать и без главной страницы

# Заполнение базы тестовыми данными
```
php artisan db:seed
```
Добавляется 50 пользователей с 500 отзывами каждый.

# Тестирование
```
php artisan test
```

# Описание
В приложении единственный роут `/api/widget/{user}`, где `user` - это индентификатор пользователя в формате uuid. Роут возвращает изображение в формате png или ошибку с соответствующим кодом. Т. к. не был до конца понятен контекст приложения, тексты и единообразие форматов ошибок я не настраивал. Для ошибок валидации (код 422) возвращается json, для всех остальных ошибок - html-страница. Коды ответов при этом всегда соответствуют коду ошибки, как и требовалось в задании.

Формат изображения выбран png исключительно для удобства тестирования. В jpg почему-то иногда немного отличается цвет изображения от того, что был передан в запрос (см. `Tests\Feature\WidgetControllerTest::test_image_custom_background_color()`). Я решил, что решение данной проблемы никак не относится к заданию, поэтому пошёл путём наименьшего сопротивления. В теории, используемая библиотека `intervention/image` позволяет выдавать изображения в разных форматах, в том числе webp.

Подробное описание роута и используемых параметров находится тут `resources/docs/swagger.yaml`

Все классы и настройки, которые не затрагивались в процессе выполнения задания, я оставил без изменений, не удалял ничего неиспользуемого и не менял форматирование на PSR-12.

Из того, что я бы добавил в первую очередь к данному приложению — это ответ `304 Not Modified` к роуту с виджетом. Но для этого нужно понимать, как определять дату последнего изменения рейтинга пользователя. Либо хранить его рейтинг в виде числа и обновлять значение непосредственно при редактировании отзывов. И вычислять рейтинг лучше с помощью хранимых процедур БД для сохранения целостности данных. Либо какие-то ещё решения, из которых можно выбирать при наличии контекста. Поэтому я решил не заниматься додумыванием дополнительных условий и оставить вычисление рейтинга пользователя непосредственно при запросе.
