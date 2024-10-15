# Проект News Portal

Этот проект представляет собой информационный портал, созданный с использованием Django. 
Проект включает функциональность для управления новостями и их отображения на сайте.


# Что сделано:
00. Активируйте виртуальное окружение:
.\venv\Scripts\activate
вы увидите зеленый (venv) в начале строки терминала

0. Запустите проект
(venv) PS C:\...\NewsPortal> python manage.py runserver

Вы по прежнему можете запускать задачи на выполнение запуская Celery Worker и Celery Beat
Запуск Celery Beat
В новом терминале выполняеься команда:
celery -A NewsPortal beat --loglevel=info

Запуск Celery Worker:
В новом терминале выполняеься команда:
celery -A NewsPortal worker --loglevel=info
*****************************************************************


Урок 34.4 Логирование
Итоговое задание 34.4.1
Настоящие системы логирования очень распределенные и орудуют большим количеством связанных компонентов. 
Давайте попробуем создать подобный механизм. Ваши настройки логирования должны выполнять следующее:

- Исправления:
* Что было изменено:
Добавлен вывод стэка ошибок (exc_info) в форматтеры, потому что он отсутсвовал:
console_error: {exc_info} добавлен для вывода стэка ошибки в консоль.
file_error: {exc_info} добавлен для вывода стэка ошибки в файл errors.log.
Эти изменения позволяют логгировать стэк ошибок, как указано в задании, для сообщений уровня ERROR и CRITICAL. 





1. В консоль должны выводиться все сообщения уровня DEBUG и выше, включающие время, уровень сообщения, сообщения. 
Для сообщений WARNING и выше дополнительно должен выводиться путь к источнику события (используется аргумент pathname в форматировании). 
А для сообщений ERROR и CRITICAL еще должен выводить стэк ошибки (аргумент exc_info). 
Сюда должны попадать все сообщения с основного логгера django.
- Сделано: Для вывода сообщений в консоль, добавлен новый formatter для форматирования сообщений в зависимости от их уровня. 
Добавлег также фильтр, чтобы сообщения выводились в консоль только при DEBUG=True.

- Исправления:
* Что было изменено:
Добавлен вывод стэка ошибок (exc_info) в форматтеры, потому что он отсутсвовал:
console_error: {exc_info} добавлен для вывода стэка ошибки в консоль.
file_error: {exc_info} добавлен для вывода стэка ошибки в файл errors.log.
Эти изменения позволяют логгировать стэк ошибок, как указано в задании, для сообщений уровня ERROR и CRITICAL. 

- Исправления:
* Объяснение изменений:
- Дочерние логгеры, такие как django.request, django.server, имели установленный уровень ERROR, 
что блокировало сообщения уровня DEBUG. 
Я изменил уровень логирования на DEBUG, чтобы обеспечить обработку всех сообщений начиная с этого уровня.
- Параметр propagate=False отключает передачу сообщений в основной логгер django, 
из-за чего обработчики в django не могли их обрабатывать. 
Чтобы это исправить, я установил propagate=True, что позволяет дочерним регистраторам передавать свои сообщения на уровень выше.



2. В файл general.log должны выводиться сообщения уровня INFO и выше только с указанием времени, уровня логирования, модуля, 
в котором возникло сообщение (аргумент module) и само сообщение. Сюда также попадают сообщения с регистратора django.
- Сделано: Для записи сообщений уровня INFO и выше в файл general.log, добавлен новый formatter и обработчик, 
который будет записывать форматированные сообщения. 
Будет использован фильтр, чтобы записывать в файл только при DEBUG=False.


3. В файл errors.log должны выводиться сообщения только уровня ERROR и CRITICAL. 
В сообщении указывается время, уровень логирования, само сообщение, путь к источнику сообщения и стэк ошибки. 
В этот файл должны попадать сообщения только из логгеров django.request, django.server, django.template, django.db.backends.
- Сделано: Создан обработчик для записи только сообщений уровня ERROR и CRITICAL в файл errors.log, 
с указанием времени, уровня сообщения, пути к источнику и стэка ошибки. 
Это будет распространяться на логгеры django.request, django.server, django.template, django.db.backends.

- Исправления:
* Что было изменено:
Удален фильтр require_debug_false из обработчика file_error. 
Теперь сообщения в файл errors.log будут записываться независимо от значения переменной DEBUG.


4. В файл security.log должны попадать только сообщения, связанные с безопасностью, а значит только из логгера django.security. 
Формат вывода предполагает время, уровень логирования, модуль и сообщение.
- Сделано: Создан отдельный обработчик для логирования сообщений безопасности в файл security.log, используя логгер django.security. 
Форматирование сообщений будет включать время, уровень логирования, модуль и само сообщение.


5. На почту должны отправляться сообщения уровней ERROR и выше из django.request и django.server по формату, как в errors.log, 
но без стэка ошибок.
- Сделано: Для отправки сообщений на почту для логгеров django.request и django.server, добавлю обработчик mail_admins, 
который будет отправлять сообщения уровня ERROR и выше, как в errors.log, но без стэка.

- Исправления:
* Что было изменено:
добавлены переменные ADMINS и SERVER_EMAIL в твой settings.py.
Как это работает вместе:
Когда происходит ошибка уровня ERROR или выше, django.utils.log.AdminEmailHandler обрабатывает это сообщение.
Этот обработчик отправляет email-сообщение на адреса, указанные в ADMINS.
Сообщение отправляется с адреса, указанного в SERVER_EMAIL.


Более того, при помощи фильтров нужно указать, что в консоль сообщения отправляются только при DEBUG = True, 
а на почту и в файл general.log — только при DEBUG = False.
- Сделано: Использую фильтры для управления отправкой сообщений в зависимости от значения DEBUG. 
В консоль сообщения выводятся только при DEBUG=True, 
а в файл general.log и на почту сообщения отправляются при DEBUG=False.





*****************************************************************
Урок 32.5. Тайные функции админ-панели Django
- Во все объекты добавьте удобный вывод и фильтры.
Добавленно для 
admin.site.register(Profile, ProfileAdmin) Рассширено с фильтрами.
admin.site.register(Article, ArticleAdmin) Рассширено с фильтрами.
admin.site.register(Category, CategoryAdmin)
admin.site.register(Rating, RatingAdmin)

*****************************************************************
Урок 32.4. Написание собственных команд
 
- Напишите команду для manage.py, которая будет удалять все новости из какой-либо категории, 
но только при подтверждении действия в консоли при выполнении команды.
Сделано
Создан файл news/management/commands/delete_category_news.py
Его можно найти по вышеуказаному пути. 
Проверил. Работает.

*****************************************************************
Урок 32.3. Команды для работы с базами данных
- Выгрузите данные с вашего новостного сайта. 
Задача 1. Выгрузить все данные с новостного сайта в json файл.
Сделано. Как сделано смотрите "Что сделано более подробно"
Задача 1а. Выгрузить все данные с новостного сайта в xml файл.
Сделано.

- Выгрузите только статьи и категории. 
Задача 2. Выгрузить только статьи и категории (все поля обоих таблиц). Только в  json файл.
Сделано.

- Удалите все данные с вашего новостного сайта и попробуйте загрузить их с ранее созданной копии.
Задача 3.  Удалить все данные из базы данных. База данных должна остаться пустой. Без данных но со всеми таблицами которые должны быть.
Сделано.

Задача 4. Загрузить в пустую базу данных данные с ранее созданной копии json файла.
Сделано. Возникла проблема с таблицей Category. 
Говорит что конфликт имён. 
Нужно заменить unique=True на unique=False.

*****************************************************************
Урок 29.4. Кэширование на низком уровне
- Дополните свой новостной портал, добавьте кеширование для статей. 
Пока статья не изменилась, она должна сохраняться в кэше.
Сделано. При редактировании и удалении кэш очищается.


*****************************************************************
29.3. Кэширование в Django
- Добавьте кэширование на страницы с новостями (по 5 минут на каждую) и на главную страницу (одну минуту).
Сделано.

- В шаблонах постарайтесь кэшировать все навигационные элементы (меню, сайдбары и т. д.). 
Сделано.
Количество кэшируемого времени остаётся на ваше усмотрение. 
Сделано для 1 минуты и для 5 мин.
Кроме того, можете использовать любую систему кэширования, какая вам более по нраву.
Сделано, использована файловая система кэширования.



# Что сделано более подробно:

*****************************************************************
# Урок 32.5. Тайные функции админ-панели Django
Во все объекты добавьте удобный вывод и фильтры.
Добавленно для 
admin.site.register(Profile, ProfileAdmin)
admin.site.register(Article, ArticleAdmin)
admin.site.register(Category, CategoryAdmin)
admin.site.register(Rating, RatingAdmin)

- Category и Rating состоят из одного поля и ID по этому ничего не добавил.

- Для admin.site.register(Profile, ProfileAdmin)
Методы для отображения полей:

user_full_name(self, obj): Возвращает полное имя пользователя, объединяя его first_name и last_name.
user_username(self, obj): Возвращает логин пользователя (username).
user_email(self, obj): Возвращает email пользователя (email).
user_groups(self, obj): Возвращает список групп, в которых состоит пользователь, разделенный запятыми.
date_joined(self, obj): Возвращает дату регистрации пользователя (date_joined).
------
Настройка полей list_display:
Поля list_display указывают, какие данные должны отображаться в таблице админки.
------
Настройка search_fields и list_filter:
Поля search_fields указывают, по каким атрибутам можно искать пользователей в админке.
Поля list_filter позволяют добавлять фильтры для быстрого поиска пользователей по группам и дате регистрации.
------
Переименование столбцов в админке:
short_description указывается для изменения заголовков столбцов в таблице админки.
------
Теперь класс ProfileAdmin настроен для отображения:

Имени и фамилии.
Логина.
Email.
Групп, в которых состоит пользователь.
Даты регистрации.

------
admin.site.register(Article, ArticleAdmin)
Метод author_name(self, obj):

Этот метод используется для вывода имени и фамилии автора. В админке он будет отображаться вместо author_profile.
Используется для вывода значения first_name и last_name пользователя (Profile связывается с User).
Метод article_type_display(self, obj):

Возвращает строку "Новость", если поле article_type равно True, и "Статья", если False.
Метод category_name(self, obj):

Проверяет наличие категории и, если она существует, выводит её имя.
Если категория не указана (None), выводится "Категория не указана".
Поля list_display:

Вместо оригинальных полей author_profile, article_type и category, используются созданные методы (author_name, article_type_display, category_name).
Переименование колонок:
Параметр short_description используется для задания пользовательского заголовка столбцов в админке (например, Имя автора, Тип публикации, Категория).

Теперь класс ArticleAdmin настроен для отображения:

Заголовок (title).
Дата публикации (publication_date).
Кастомное отображение типа статьи (article_type_display).
Кастомное отображение имени и фамилии автора (category_name).
Кастомное отображение типа публикации (Новость или Статья, article_type_display).
Имя категории или сообщение, если категория не указана (category_name).
Содержание поста (content).


*****************************************************************
# Урок 32.4. Написание собственных команд
 
Напишите команду для manage.py, которая будет удалять все новости из какой-либо категории, 
но только при подтверждении действия в консоли при выполнении команды.
Сделано
Создан файл news/management/commands/delete_category_news.py
Его можно найти по вышеуказаному пути. 
Проверил. Работает.


*****************************************************************
# Урок 32.3. Команды для работы с базами данных


Выгрузите данные с вашего новостного сайта. 
Задача 1. Выгрузить все данные с новостного сайта в json файл.
Создан файл 
export_all_to_json.py
в файле нужно указать действительный путь к базе данных.

Задача 1а. Выгрузить все данные с новостного сайта в xml файл.
Создан файл 
export_all_to_xml.py
в файле нужно указать действительный путь к базе данных.


Выгрузите только статьи и категории. 
Задача 2. Выгрузить только статьи и категории (все поля обоих таблиц). Только в  json файл.
Создан файл 
export_articles_and_categories_to_json.py
в файле нужно указать действительный путь к базе данных.


Удалите все данные с вашего новостного сайта и попробуйте загрузить их с ранее созданной копии.
Задача 3.  Удалить все данные из базы данных. База данных должна остаться пустой. Без данных но со всеми таблицами которые должны быть.
Создан файл 
clear_database.py
в файле нужно указать действительный путь к базе данных.

Задача 4. Загрузить в пустую базу данных данные с ранее созданной копии json файла.
Создан файл 
import_from_json.py
в файле нужно указать действительный путь к базе данных.
Предпологается что all_data.json находится в той папке откуда задаётся команда на выполнение файла import_from_json.py.



*****************************************************************
# Урок 29.4. Кэширование на низком уровне


- Дополните свой новостной портал, добавьте кеширование для статей. 
Пока статья не изменилась, она должна сохраняться в кэше.
Сделано следующее:
1. изменено def article_detail(request, id)
def article_detail(request, id):
    article = get_object_or_404(Article, id=id)
    return render(request, 'news/article_detail.html', {'article': article})

на
def article_detail(request, id):
    article = get_object_or_404(Article, id=id)
    cache_key = get_article_cache_key(article.id, article.publication_date)

    # Если кэш для этой статьи существует, берем из кэша
    cached_content = cache.get(cache_key)
    if cached_content:
        return cached_content

    # Если кэш не найден, рендерим и сохраняем в кэш
    response = render(request, 'news/article_detail.html', {'article': article})
    cache.set(cache_key, response, 300)  # Кэшируем на 5 минут

2. создан новый файл utils.py и в нем функция 
def get_article_cache_key(article_id, publication_date):
    """
    Создаёт уникальный ключ для кэширования статьи на основе её ID и даты последнего изменения.
    """
    return f"article_{article_id}_{publication_date}"


Добавлен код
    if request.method == 'POST':
        # Очистка кэша перед записью в базу данных
        cache_key = get_article_cache_key(article.id, article.publication_date)
        cache.delete(cache_key)
        print(f"Кэш для статьи {article.id} был удален перед записью в базу данных.")
		
в def edit_post(request, pk): и def delete_post(request, pk):
для того чтобы при изменении и удалении статьи кэш стерся.


****************************************************************
# Урок 29.3. Кэширование в Django

- Добавьте кэширование на страницы с новостями (по 5 минут на каждую) и на главную страницу (одну минуту).
1. Установил  django-redis и memcached
pip install django-redis python-memcached

2. В settings.py добавь настройки для нескольких кэш-систем:
CACHES = {
    'default': {
        # 'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache', # Стандартные инструменты django
        'BACKEND': 'news.custom_cache.LoggingFileBasedCache',  # Указываем путь до кастомного кэша
        'LOCATION': os.path.join(BASE_DIR, 'cache'),  # Путь к директории для хранения кэша
        'OPTIONS': {
            'MAX_ENTRIES': 1000,
        },
    },
    'redis_cache': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://127.0.0.1:6379/1',
        'OPTIONS': {
            'CLIENT_CLASS': 'django_redis.client.DefaultClient',
        }
    },
}




3. В views.py и добавлены кэширование для представлений с помощью декоратора cache_page для  представлениям article_list и home.
from django.views.decorators.cache import cache_page

@cache_page(60 * 5, cache='redis_cache')  # Кэширование на 5 минут, используя Redis
def article_list(request):
    # Код представления
    ...

@cache_page(60, cache='memcached_cache')  # Кэширование на 1 минуту, используя Memcached
def home(request):
    # Код представления
    ...


- В шаблонах постарайтесь кэшировать все навигационные элементы (меню, сайдбары и т. д.). 
Количество кэшируемого времени остаётся на ваше усмотрение. 
Кроме того, можете использовать любую систему кэширования, какая вам более по нраву.

4. Добавление фрагментного кэширования в default.html:

В default.html использовал тег {% cache %} для кэширования частей страницы.
<!--Навигационная панель (header) кэшируется на 1 час (3600 секунд) для снижения нагрузки на рендеринг.-->
    {% load cache %}
    {% cache 3600 header %}
	.............
	.............	
<!--Конец кэширования навигационной панели (header)-->
    {% endcache %}
	

	
	
<!--Футер (footer), кэшируется на 1 час (3600 секунд), так как данная часть страницы меняется редко.-->
    {% cache 3600 footer %}
	.............
	.............
<!--Конец кэширования Футер (footer).-->
    {% endcache %}


*****************************************************************
*****************************************************************
*****************************************************************

# Итоговое задание (HW-03) 28

Что сделано кратко:

1. Установить Redis. Сделано
2. Установить Celery. Сделано
3. Произвести необходимые конфигурации Django для соединения всех компонентов системы.
Сделано

4. Реализовать рассылку уведомлений подписчикам после создания новости.
В tasks.py я добавили задачу send_new_article_notification, 
которая будет получать article_id в качестве аргумента и асинхронно отправлять письма подписчикам.
Буду использовать уже имеющийся метод EmailContentBuilder.generate_notification_email для формирования содержимого письма.

Добавил параметр USE_CELERY в settings.py для переключения между синхронной и асинхронной отправкой
USE_CELERY = True  # Установите False, если хотите использовать синхронные уведомления
Подробнее можно увидеть в разделе "Что сделано подробнее:" с 4 по 8 пункты.
Так же использовал USE_CONSOLE_EMAIL_BACKEND = True  # True для тестов, False для реальной отправки писем.

5. Реализовать еженедельную рассылку с последними новостями (каждый понедельник в 8:00 утра).
Реализовано.
Для тестовой проверки эту задачу сделал чтобы присылала письма в консоль используя переключатель USE_CONSOLE_EMAIL_BACKEND = True,
и чтобы это происходило каждую минуту.

Настройки периодических задач:

CELERY_BEAT_SCHEDULE = {

    'send-weekly-digest-every-monday-8am': {
        'task': 'news.tasks.send_weekly_digest',  # Путь к задаче
        # 'schedule': crontab(hour=8, minute=0, day_of_week='monday'),  # Каждый понедельник в 8:00 утра
        'schedule': crontab(minute='*/1') # Каждую минуту:

    },
}


**********************************************************************************************
Что сделано подробнее:
0. Активируйте виртуальное окружение:
.\venv\Scripts\activate
вы увидите зеленый (venv) в начале строки терминала

1. Установил Celery и дополнительные зависимости
pip install celery[redis]

2. Установил django-celery-beat и django-celery-results, 
так как я планирую управлять периодическими задачами и сохранять результаты задач в базе данных:
pip install django-celery-beat django-celery-results

Скачал Redis для Windows и установил его.
Так же настроил Path к серверу Redis.
За тем с  помощью команды redis-server запустил сервер.

3. Создал файл celery.py в директории моего Django проекта (где находится settings.py)
в корне проекта, NewsPortal/celery.py.

****************************************
NewsPortal/celery.py 

from __future__ import absolute_import, unicode_literals
import os
from celery import Celery
from django.conf import settings

- Установка переменной окружения для настройки Django
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'NewsPortal.settings')

- Создание экземпляра Celery
app = Celery('NewsPortal')

- Загружаем настройки из settings.py и указываем префикс "CELERY" для всех настроек Celery
app.config_from_object('django.conf:settings', namespace='CELERY')

- Автоматическое определение задач (tasks.py) в приложениях
app.autodiscover_tasks(lambda: settings.INSTALLED_APPS)

- Простейшая тестовая задача
@app.task(bind=True)
def debug_task(self):
    print(f'Request: {self.request!r}')

- NewsPortal/celery.py 
****************************************************************

4. Включил Celery в проекте Django.
добавид следующие настройки в settings.py:

- Настройки Celery в settings.py
CELERY_BROKER_URL = 'redis://127.0.0.1:6379/0'  # Адрес брокера сообщений Redis
CELERY_RESULT_BACKEND = 'redis://127.0.0.1:6379/0'  # Хранилище результатов выполнения задач\n
#Если вы хотите использовать RabbitMQ, замените строку на:
#CELERY_BROKER_URL = 'amqp://guest:guest@localhost//'
#CELERY_RESULT_BACKEND = 'django-db'  # Хранение результатов в базе данных
#Хранилище результатов задач (например, Redis или другой бекенд)
#CELERY_RESULT_BACKEND = 'redis://127.0.0.1:6379/0'
#CELERY_CACHE_BACKEND = 'django-cache'  # Хранение кэшированных данных в Django
CELERY_ACCEPT_CONTENT = ['json']  # Допустимый формат данных
CELERY_TASK_SERIALIZER = 'json'  # Сериализация задач
CELERY_RESULT_SERIALIZER = 'json'  # Сериализация результатов
CELERY_TIMEZONE = 'UTC'  # Временная зона

- модуль, где находятся задачи
CELERY_IMPORTS = (
    'news.tasks',  # В приложение `news`
)

- Настройки периодических задач

CELERY_BEAT_SCHEDULE = {
     #'add-every-30-seconds': {
         #'task': 'news.tasks.add',
         #'schedule': 30.0,
         #'args': (16, 16)
     #},
     #'send-test-email-every-minute': {
         #'task': 'news.tasks.send_test_email',
         #'schedule': 60.0,  # Выполняется каждую минуту
     #},
    'send-weekly-digest-every-monday-8am': {
        'task': 'news.tasks.send_weekly_digest',  # Путь к задаче
         #'schedule': crontab(hour=8, minute=0, day_of_week='monday'),  # Каждый понедельник в 8:00 утра
         #'schedule': crontab(hour=17, minute=7, day_of_week='monday'),  # Каждый понедельник в 17:07 
         #'schedule': crontab(hour=8, minute=0) # Каждый день в 8:00 утра
         #'schedule': crontab(minute='*/15') # Каждые 15 минут
         #'schedule': crontab(hour=12, minute=0, day_of_week='fri', month_of_year='1,12') # Каждую первую и последнюю пятницу месяца в 12:00 дня
        
        'schedule': crontab(minute='*/1') # Каждые 1 минут:

    },
}


5. Обновление файла __init__.py (инициализация Celery).
- NewsPortal/__init__.py

from .celery import app as celery_app  # Импортируем приложение Celery

__all__ = ('celery_app',)

6. В tasks.py записал новое тестовое задание.
@shared_task
def send_test_email():
    subject = 'Тестовое письмо'
    message = 'Это тестовое письмо от Celery.'
    recipient_list = ['example@example.com']
    send_mail(subject, message, settings.EMAIL_HOST_USER, recipient_list)
    return "Тестовое письмо отправлено успешно!"

7. Настройка django-celery-beat для периодических задач.
В settings.py добавьте django_celery_beat в список установленных приложений
INSTALLED_APPS = [
    ...
    'django_celery_beat',
]

- Выполнил миграции для django-celery-bea
python manage.py migrate django_celery_beat

- Запустил сервер для проверки.
python manage.py runserver

8. Запуск Celery Worker и Celery Beat
Запуск Celery Beat
В новом терминале выполняеься команда:
celery -A NewsPortal beat --loglevel=info

Запуск Celery Worker:
В новом терминале выполняеься команда:
celery -A NewsPortal worker --loglevel=info

Проблемы совместимости Celery и billiard с Windows: Модуль billiard, 
который используется в Celery для многопоточности, иногда сталкивается с проблемами на Windows, 
особенно при использовании метода fork (создания нового процесса).
По этому найденное решение 
celery -A NewsPortal worker --loglevel=info --pool=solo
Использование метода запуска solo. Запустил Celery с использованием метода solo, 
который работает на Windows и избегает использования billiard и заработало.

так же добавил в celery.py эту конфигурацию.

<!-- # Добавляем конфигурацию worker_pool для режима 'solo' -->
app.conf.update(
    worker_pool='solo'  # Используем 'solo' в качестве метода pool
)

По этому теперь можно ввести просто celery -A NewsPortal worker --loglevel=info,
а не celery -A NewsPortal worker --loglevel=info --pool=solo. 


Таким образом реализована еженедельная рассылка с последними новостями (каждый понедельник в 8:00 утра).
Для тестовой проверки эту задачу сделал чтобы присылала письма в консоль и чтобы это происходило каждую минуту.

<!-- # Настройки периодических задач -->
CELERY_BEAT_SCHEDULE = {

    'send-weekly-digest-every-monday-8am': {
        'task': 'news.tasks.send_weekly_digest',  # Путь к задаче
        # 'schedule': crontab(hour=8, minute=0, day_of_week='monday'),  # Каждый понедельник в 8:00 утра
        # 'schedule': crontab(hour=17, minute=7, day_of_week='monday'),  # Каждый понедельник в 17:07 
		# 'schedule': crontab(hour=8, minute=0) # Каждый день в 8:00 утра
		# 'schedule': crontab(minute='*/15') # Каждые 15 минут
		# 'schedule': crontab(hour=12, minute=0, day_of_week='fri', month_of_year='1,12') # Каждую первую и последнюю пятницу месяца в 12:00 дня
        'schedule': crontab(minute='*/1') # Каждую минуту:

    },
}



# Итоговое задание (HW-03) 27.5
По итогу работы в модуле было усовершенствовано новостное приложение согласно требованиям:

1. В категории должна быть возможность пользователей подписываться на рассылку новых статей в этой категории.
Сделано!

2. Если пользователь подписан на какую-либо категорию, то, как только в неё добавляется новая статья, её краткое содержание приходит пользователю на электронную почту, которую он указал при регистрации. В письме обязательно должна быть гиперссылка на саму статью, чтобы он мог по клику перейти и прочитать её.
Сделано!

3. Если пользователь подписан на какую-либо категорию, то каждую неделю ему приходит на почту список новых статей, появившийся за неделю с гиперссылкой на них, чтобы пользователь мог перейти и прочесть любую из статей.
Сделаны оба варианта и по времени, например в каждое воскресение в 00:00, 
и по продолжительности, например через каждые 7 дней.
Для тестирования сделан вариант когда оба варианта срабатывают через 1 минуту.


4. Добавьте приветственное письмо пользователю при регистрации в приложении.
Сделано!

*******************************************************************
Что сделано более подробно

1. Модификация модели Category:
Добавление поля subscribers в модель Category для хранения пользователей, подписанных на категорию.
Отправка email-уведомления подписчикам категории:
При добавлении новой статьи в категорию всем подписчикам на email будет отправляться письмо с темой, 
содержащей заголовок статьи, и текстом письма, включающим приветствие, заголовок и первые 50 символов текста статьи.
Реализация возможности подписки на сайте:
Если пользователь авторизован, проверяется, подписан ли он на категорию через условие {% if user in article.category.subscribers.all %}.
Если подписан, выводится сообщение: «Вы подписаны на категорию ...».
Если не подписан, появляется кнопка для подписки на странице article_detail.html, чтобы пользователь мог подписаться на категорию..
Если пользователь не авторизован, выводится сообщение о необходимости авторизации для подписки с ссылкой на страницу входа.
При подписке пользователя связывается с категорией через поле subscribers.
После подписки кнопка исчезает и появляется текст «Вы подписаны на категорию ...».
Отправление письма выделено в отдельную функцию и по этому,
реализованы 2 варианта - когда сообщение отправляеться на реальный адрес и 
когда сообщение отправляеться в консоль для тестирования через отдельную функцию.
Для этого в .env файл меняем переменую 
USE_CONSOLE_EMAIL_BACKEND = True  
True для тестов, 
False для реальной отправки писем

2. Добавление функции send_notification для всех подписчиков при создании нового поста.
Реализованы 2 варианта - когда сообщение отправляеться на реальный адрес и 
когда сообщение отправляеться в консоль для тестирования через отдельную функцию.
Для этого в .env файл меняем переменую 
USE_CONSOLE_EMAIL_BACKEND = True  
True для тестов, 
False для реальной отправки писем

3.
Объединил edit_news с edit_article в общую функцию edit_post. 
Объединил delete_news с delete_article в общую функцию delete_post. 
Объединил create_news с create_article в общую функцию create_post.
Далее намерен объединить их в класс. 

4.Добавлено приветственное письмо, которое отправляется пользователю при регистрации.
примерно такого содержания:
Добро пожаловать на наш сайт!"
 Здравствуй, {user.username}!
 
 Спасибо за регистрацию на нашем сайте. Пожалуйста, активируйте свой аккаунт, перейдя по следующей ссылке:
 http://127.0.0.1:8000/activate/{user.pk}
 
 С уважением,,
 Команда сайта"
            
В письме есть ссылка на активацию и имя пользователя!

Создан класс EmailContentBuilder, который будет инкапсулировать всю логику формирования писем. 
В этом классе будет несколько методов, каждый из которых отвечает за создание содержимого для определенного типа письма.

Методы для формирования письма в классе EmailContentBuilder:

Методы для приветственного письма при регистрации:
generate_welcome_email: Для всех вариантов приветственных писем при регистрации.
variant1: Простой приветственный текст и ссылка на активацию.
variant2: Содержит полную информацию о профиле и ссылка на активацию..
variant3: Содержит только основные поля профиля, ссылка на активацию и приглашение изменить пароль при первом входе.
Метод для письма о подписке:
generate_subscription_email: Письмо, которое отправляется при подписке на категорию. Содержит информацию о категории и приветствие.
Метод для уведомления о новой статье:
generate_notification_email: Письмо, которое отправляется подписчикам категории при добавлении новой статьи. Содержит информацию о статье и категории.
Метод для уведомления о том что достигнут лимит по написанию постов за этот день:
def generate_limit_email(user, post_limit, group_name):
Метод для письма о новых постах появившихся ха прошедшую неделю:
def generate_weekly_digest_email(category, new_articles):


Интеграция класса:
Функции register, subscribe_to_category и send_notification будут использовать соответствующие методы класса EmailContentBuilder для генерации текста письма.
Сформированные письма будут передаваться в send_custom_email для отправки где 
Реализованы 2 варианта - когда сообщение отправляеться на реальный адрес и 
когда сообщение отправляеться в консоль для тестирования через отдельную функцию.
Для этого в .env файл меняем переменую 
USE_CONSOLE_EMAIL_BACKEND = True  
True для тестов, 
False для реальной отправки писем


Так же на регистрационной форме убрана возможность стать Author и войти в группу Premium.
При регистрации новый пользователь автоматически включается только в группу Basic.

В форму редактирования профиля осталось возможность перейти в группу Premium и стать Author.
Пока пользователь не активизирует свой акаунт по полученной ссылке он является неактивным и 
имеет такие же права как и незарегистрированный пользователь.

4. При создании новости подписчикам этой категории автоматически отправляется сообщение о пополнении в разделе. 
Содержание письма 
        # Используем имя и фамилию подписчика, если они указаны, иначе используем "Здравствуй!"
        greeting = f"Здравствуй, {subscriber.first_name} {subscriber.last_name}!" if subscriber else "Здравствуй!"

        # Формируем краткое содержание письма
        message = (f"{greeting}\n\n"
                   f"В разделе {category.name} появилась новая публикация:\n\n"
                   f"Название: {article.title}\n"
                   f"Автор: {article.author_profile.user.get_full_name() or article.author_profile.user.username}\n\n"
                   f"Краткое содержание:\n"
                   f"{article.content[:50]}...\n\n"
                   f"Перейдите по ссылке, чтобы прочитать полностью: http://127.0.0.1:8000/news/{article.pk}\n\n"
                   f"С уважением,\n"
                   f"Команда сайта")


5. Один пользователь не может публиковать более трёх новостей в сутки, если он обычный пользователь из
не более 5 если он пользователь Premium. После достижения 3 написаных статей в сутки для пользователя группы basic
и 5 для пользователя группы premium перенаправляется на страницу с сообщением

"Превышен лимит публикаций
Вы не можете публиковать более 3 постов в сутки." - для пользователя группы basic

"Превышен лимит публикаций
Вы не можете публиковать более 5 постов в сутки." - для пользователя группы premium.

Так же отпрваляется письмо уведомление с текстом для пользователя группы basic:
"
Здравствуй, {user.first_name} {user.last_name}

Вы не можете публиковать более 3 постов в сутки.
Для получения лимита в 5 постов в сутки, перейдите в группу Premium.

С уважением,
Команда сайта"

и письмо уведомление с текстом для пользователя группы premium: 

Здравствуй, {user.first_name} {user.last_name}

Как пользователь группы Premium, Вы не можете публиковать более 5 постов в сутки.

С уважением,
Команда сайта"

Реализованы 2 варианта - когда сообщение отправляеться на реальный адрес и 
когда сообщение отправляеться в консоль для тестирования через отдельную функцию.
Для этого в .env файл меняем переменую 
USE_CONSOLE_EMAIL_BACKEND = True  
True для тестов, 
False для реальной отправки писем

6. Реализована возможность смены пароля когда пользователь забыл её.
Использованы стандартные возможности Django.

Кому интересно конец.
*******************************************************************



# Структура проекта

- **Главная страница**: Показывает приветственное сообщение и ссылки на разные части сайта.
- На верхней полосе стоит меню 
Главная Публикации Поиск Новость + Статья + Панель Администратора Язык:RU имя_пользователя Вход Профиль
- Меню Публикации выдает все публикации сортируя их по дате.
Отображает список всех новостей с заголовками, датами публикаций и кратким содержанием.
Каждая новость имеет ссылку на полное содержание.
На странице с содержанием не сделана возможность ставить оценку которая влияет на рейтинг или
какой-нибудь механизм который бы считал сколько раз читали эту публикацию что так-же влияло бы на 
рейтинг. Но это пока и не требуется.
Когда кликается на читать полностью открывается страница с полным содержанием выбранной публикации.
- Меню Поиск даёт возможность найти статьи по названию, содержанию, рейтингу, автору, категории, типу и дате.
Когда остается только одна публикацию и пользователь является владельцем публикации, 
то появляются кнопки редактировать и удалить и он может редактировать или удалить публикацию.
- Меню Новость + открывает страницу для создания новости если пользователь является автором и он залогинился, 
иначе направляется на стракицу где сообщается что у вас нет достаточно прав.
- Меню Статья + открывает страницу для создания статьи если пользователь является автором и он залогинился, 
иначе направляется на стракицу где сообщается что у вас нет достаточно прав.
- Панель Администратора открывает Панель Администратора в которую можно войти только логином администратора.
- Язык:RU еще не работает.
- имя_пользователя высвечивается после того как пользователь вошел.
- Вход для того чтобы войти на сайт как пользователь.
Когда пользователь вошел то надпись меняется на Выход и осуществляет выход.
При клике на Вход - выводится страниц с логином и паролем, и дает возможность зарегистрироваться. 
Ссылка на забыл пароль еше не работает.

На странице логина есть ссылка на регистрацию. 
При регистрации пользователь может выбрать быть читателем или писателем.
Автоматически записывается в группу Basic. Но если он хочет может выбрать Premium.
При выборе Premium нужно заполнить данные банковкой карточке и только. 
Механизм не разработан. Просто стоит такая игрушка.
Также осуществлено регистрация через акаунт Yandex.
В админке группа Basic имеет право только смотреть.
Группа Author(писатель) может писать, редактировать и удалять статьи.
После регистрации переходит на страницу Профиль.
- Меню Профиль открывает страницу профиля где пользователь может радактировать свой профиль или изменить пароль. 
Удаление профиля юзера еще не реализовал.



Что сделано:

1.В классе-представлении редактирования профиля добавить проверку аутентификации.
- Проверяется аутентификация для - создания, редактирования и удаления публикации.
А также требуется аутентификация для того чтобы войти в Профиль и для редактирования Профиля.
В случае что пользователь не вошёл в систему и пытается создать, редактировать и удалить публикацию то 
перенаправляется на страницу с сообщением что у него недостаточно прав и потом на страницу логина.

2. Выполнить необходимые настройки пакета allauth в файле конфигурации.
Выполнено

3. В файле конфигурации определить адрес для перенаправления на страницу входа в систему и адрес перенаправления после успешного входа.
Выполнено.

4. Реализовать шаблон с формой входа в систему и выполнить настройку конфигурации URL.
Сделано

5. Реализовать шаблон страницы регистрации пользователей.
Сделано

6. Реализовать возможность регистрации через Yandex-аккаунт.
Реализовал. 
на Google не получается

7. Создать группы common и authors.
Сделаны группы basic, premium и authors.

8. Реализовать автоматическое добавление новых пользователей в группу common.
Сделано.

9. Создать возможность стать автором (быть добавленным в группу authors).
Сделано.

10. Для группы authors предоставить права создания и редактирования объектов модели Post (новостей и статей).
Сделано. Я назвал модель Article и не стал её менять. 

11. В классах-представлениях добавления и редактирования новостей и статей добавить проверку прав доступа.
Сделано. Только у меня просто функции а не классы.

12. Исходный код залить в git-репозиторий.
Сделано.