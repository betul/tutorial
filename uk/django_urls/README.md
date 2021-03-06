# Django url

Ми збираємося побудувати нашу першу веб-сторінку - домашню сторінку для вашого блогу! Але спочатку, давайте трохи дізнаємось про Django URL-адреси.

## Що таке URL?

URL - це просто веб-адреса. Відображається URL кожен раз, коли ви відвідуєте веб-сайт - це видно в адресному рядку браузера (так! `127.0.0.1:8000` - це URL! І `https://djangogirls.com` також є URL):

![Url](images/url.png)

Кожна сторінка в Інтернеті потребує свого власного URL. Таким чином ваш додаток знає, що треба показувати користувачу, який відкриває URL. В Django використовується така річ, яка називається `URLconf` (URL конфігурація). URLconf являє собою набір шаблонів, які Django намагатиметься зіставити з отриманих URL, щоб знайти правильне відображення.

## Як працює URL в Django?

Давайте відкриємо файл `mysite/urls.py` в редакторі коду і подивимось, як він виглядає:

```python
from django.conf.urls import include, url
from django.contrib import admin

urlpatterns = [
    # Examples:
    # url(r'^$', 'mysite.views.home', name='home'),
    # url(r'^blog/', include('blog.urls')),

    url(r'^admin/', include(admin.site.urls)),
]
```

Як бачите, Django вже щось записав сюди для нас.

Рядки, що починаються з `#` - це коментарі, тобто ці рядки не будуть запускатися Python. Досить зручно, чи не так?

URL панелі адміністратора, котрий ви відвідували у попередньому розділі вже є тут:

```python
    url(r'^admin/', include(admin.site.urls)),
```

Це означає, що для кожного URL, що починається з `admin/` Django знаходитиме відповідне відображення - *view*. У даному випадку ми включаємо багато адміністраторських URL, отже не все запаковується в цей маленький файл, що є більш читабельним і акуратнішим.

## Regex

Хіба вам не цікаво, яким чином Django створює відповідності між URL і відображеннями? Гаразд, ця частина досить хитра. Django використовує `regex`, що є скороченням від "regular expressions" (регулярні вирази). Regex має багато (багато!) правил, що формують шаблон пошуку. Оскільки регулярні вирази є продвинутою темою, ми не будемо вникати в деталі їх роботи.

Якщо ви все ще хочете зрозуміти, як ми створили шаблони, ось приклад процесу - нам потрібно буде тільки обмежена підмножина правил для того, щоб виразити потрібний шаблон, а саме:

    ^ для початку тексту
    $ для кінця тексту
    \d для цифри
    + щоб позначити, що попередній символ має бути повторений мінімум один раз
    () для захоплення частини шаблону

Все інше у визначенні url буде прийматися як є.

Тепер уявіть, що у вас є сайт з такою адресою: `http://www.mysite.com/post/12345/`, де `12345` - це номер вашого посту.

Написання окремих відображень для усіх номерів постів було б дійсно нестерпним. За допомогою регулярних виразів ми можемо створити шаблон, який відповідає url і витягти номер для нас: `^post/(\d+)/$`. Давайте розберемо його по частинах, щоб побачити, що ми тут робимо:

* **^post/** говорить Django взяти що-небудь, що має `post/` на початку url (зразу після `^`)
* **(\d+)** означає, що буде число (одне або декілька цифр), і ми хочемо, щоб число захопилось і вибралось
* **/** вказує Django, що наступним символом має бути `/`
* **$** далі позначає кінець URL-адреси, а це означає, що під шаблон підходять лише ті рядки, які закінчуються на `/`


## Ваш перший Django url!

Час створити наш перший URL! Ми хочемо, щоб 'http://127.0.0.1:8000/' був домашньою сторінкою нашого блогу і виводив список постів.

Також ми б хотіли підтримувати порядок у файлі `mysite/urls.py`, отже імпортуємо url з нашого додатку `blog` у файл `mysite/urls.py`.

Вперед, видаліть закоментовані рядки (рядки, що починаються з `#`) та додайте рядок, що імпортує `blog.urls` до головного url (`''`).

Ваш файл `mysite/urls.py` повинен наразі мати такий вигляд:

```python
from django.conf.urls import include, url
from django.contrib import admin

urlpatterns = [
    url(r'^admin/', include(admin.site.urls)),
    url(r'', include('blog.urls')),
]
```

Django тепер перенаправлятиме усе, що надходить на 'http://127.0.0.1:8000/' до `blog.urls` і шукатиме там подальші інструкції.

Для запису регулярних виразів у Python використовується символ `r` перед стрічкою. Це підказка для інтерпретатора, що рядок може містити спеціальні символи, які використовуються в регулярних виразах.


## blog.urls

Створіть новий порожній файл `blog/urls.py`. Добре! Додайте наступні перші два рядки:

```python
from django.conf.urls import url
from . import views
```

Тут ми лише імпортуємо методи Django і всі наші відображення з додатку `blog` (у нас ще немає жодного, але ми повернемося до цього через хвилину!)

Після цього, ми можемо додати наш перший URL шаблон:

```python
urlpatterns = [
    url(r'^$', views.post_list, name='post_list'),
]
```

Як бачите, ми присвоюємо відображення із назвою `post_list` значенню URL `^$`. Цей регулярний вираз буде перевіряти рядок на `^` (початок рядка) та `$` (кінець) - тому тільки порожній рядок буде відповідати цим вимогам. Це правильно, тому що в механізмі Django URL, вираз ' http://127.0.0.1:8000 /' не є частиною URL-адреси. Цей шаблон показуватиме для Django, що `views.post_list` є правильним місцем для переходу, якщо хтось введе ваш сайт за адресою 'http://127.0.0.1:8000/'.

На останок, `name='post_list'` - це назва URL, який використовуватиметься для ідентифікації потрібного відображення (в'ю). Це може бути ім'ям потрібного нам відображення (в'ю), або взагалі чимось іншим. Ми буде використовувати іменовані URL-адреси, напротязі всього проекту, тому важливо, давати ім'я кожному URL у застосунку. Ми також повинні намагатися давати URL-адресам якомога прості на унікальні імена, так, щоб їх було легко запам'ятати.

Усе гаразд? Відкрийте http://127.0.0.1:8000/ у браузері, щоб побачити результат.

![Error](images/error1.png)

Що, тут більше немає повідомлення "It works"? Не хвилюйтесь, це просто сторінка із помилкою, нічого боятися! Вони є насправді дуже корисними:

Можете прочитати тут: __no attribute 'post_list'__. Чи *post_list* не нагадує вам про щось? Це назва нашого відображення! Це означає, що все знаходиться на місці, але ми просто ще не створили наше *відображення*. Не переймайтесь, ми його отримаємо.

> Якщо бажаєте дізнатися більше про Django URLconf, зверніться до офіційної документації: https://docs.djangoproject.com/en/1.8/topics/http/urls/
