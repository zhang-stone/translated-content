---
title: 'Руководство Django часть 4: административная панель Django'
slug: Learn/Server-side/Django/Admin_site
translation_of: Learn/Server-side/Django/Admin_site
---
{{LearnSidebar}}{{PreviousMenuNext("Learn/Server-side/Django/Models", "Learn/Server-side/Django/Home_page", "Learn/Server-side/Django")}}

Теперь, когда модели для сайта [местной библиотеки](/ru/docs/Learn/Server-side/Django/Tutorial_local_library_website) созданы, добавим некоторые "настоящие" данные о книгах, используя административную панель Django Admin. Для начала мы покажем, как зарегистрировать в ней модели, потом как войти и создать какие-нибудь данные. В конце статьи мы покажем некоторые способы дальнейшего улучшения вида админ-панели.

| Предусловия: | Сначала завершите: [Руководство часть 3:](/ru/docs/Learn/Server-side/Django/Models) использование моделей.                |
| ------------ | ------------------------------------------------------------------------------------------------------------------------- |
| Цель:        | Уяснить преимущества и ограничения админ-панели Django, научиться использовать её для создания записей для наших моделей. |

## Обзор

_Приложение_ Django admin может использовать ваши модели для автоматического создания части сайта, предназначенной для создания, просмотра, обновления и удаления записей. Это может сэкономить вам много времени в процессе разработки, упрощая тестирование ваших моделей на предмет _правильности_ данных. Оно также может быть полезным для управления данными на стадии публикации, в зависимости от типа веб-сайта. Проект Django рекомендует это приложение только для управления внутренними данными (т.е.для использования администраторами, либо людьми внутри вашей организации), так как модельно-ориентированный подход не обязательно является наилучшим интерфейсом для всех пользователей и раскрывает много лишних подробностей о моделях.

Все необходимые настройки, которые необходимо включить в admin приложение вашего веб-сайта, были сделаны автоматически, когда вы [создали каркас проекта](/ru/docs/Learn/Server-side/Django/skeleton_website) ( информацию о необходимых актуальных зависимостях смотрите здесь - [Django docs](https://docs.djangoproject.com/en/1.10/ref/contrib/admin/)) . В результате все, что необходимо сделать для того, чтобы добавить модели в приложение admin, это _зарегистрировать_ их. В конце этой статьи мы представим краткую демонстрацию того, каким образом можно дополнительно настроить админ-панель для лучшего отображения данные наших моделей.

После регистрации моделей мы покажем как создать нового суперпользователя , войти на сайт от его имени и создать книги, авторов, экземпляры книг и жанры. Это будет полезным для тестирования представлений и шаблонов, которые мы начнём создавать в следующей части руководства.

## Регистрация моделей

Вначале откройте файл **admin.py** в папке приложения (**/locallibrary/catalog/admin.py**). Пока он выглядит так (заметьте, что он уже содержит импорт `django.contrib.admin)`:

```python
from django.contrib import admin

# Register your models here.
```

Зарегистрируйте модели путём вставки следующего текста в нижнюю часть этого файла. Этот код просто импортирует модели и затем вызывает `admin.site.register` для регистрации каждой из них.

```python
from .models import Author, Genre, Book, BookInstance

admin.site.register(Book)
admin.site.register(Author)
admin.site.register(Genre)
admin.site.register(BookInstance)
```

> **Примечание:** Если вы приняли участие в создании модели для представления естественного языка книги ([см. обучающую статью о моделях](/ru/docs/Learn/Server-side/Django/Models)), импортируйте и зарегистрируйте её тоже!

Это самый простой способ регистрации модели или моделей. Админ-панель имеет множество настроек. Мы рассмотрим другие способы регистрации ваших моделей ниже.

## Создание суперпользователя

Для того, чтобы войти в админ-панель, нам необходимо иметь учётную запись пользователя со статусом _Staff (сотрудники)._ Для просмотра и создания записей, пользователю также понадобится разрешение для управления всеми нашими объектами. Вы можете создать учётную запись "superuser", которая даёт полный доступ к сайту и все необходимые разрешения, используя **manage.py**.

Для создания суперпользователя вызовите следующую команду из той же папки, где расположен **manage.py**. Вас попросят ввести имя пользователя, адрес электронной почты и надёжный пароль.

```bash
python3 manage.py createsuperuser
```

После выполнения этой команды новый суперпользователь будет добавлен в базу данных. Теперь перезапустите сервер, чтобы можно было протестировать вход на сайт:

```bash
python3 manage.py runserver
```

## Вход в админ-панель и её использование

Для входа в админ-панель откройте ссылку _/admin_ (например [http://127.0.0.1:8000/admin](http://127.0.0.1:8000/admin/)) и введите логин и пароль вашего нового суперпользователя (вас перенаправят на login-страницу и потом обратно на /admin после ввода всех деталей).

В этой части сайта отображаются все наши модели, сгруппированные по установленному приложению. Вы можете кликнуть на названии модели, чтобы получить список всех связанных записей, далее можете кликнуть на этих записях, для их редактирования . Также можно непосредственно кликнуть на ссылку **Add**, расположенную рядом с каждой моделью, чтобы начать создание записи этого типа.

![Admin Site - Home page](https://mdn.mozillademos.org/files/13975/admin_home.png)

Кликните на ссылке **Add** справа от _Books_, чтобы создать новую книгу (появится диалоговое окно как на картинке внизу). Заметьте, что заголовок каждого поля - это тип используемого виджета, и `help_text` (если есть) совпадает со значением, которое вы указали в модели.

Введите значение для полей. Вы можете создавать новых авторов или жанры, нажимая на значок "+ ", расположенный рядом с соответствующим полем (или выберите существующее значение из списков, если вы уже создали их). Когда вы закончили, нажмите на **SAVE,** **Save and add another**, или **Save and continue editing,** чтобы сохранить записи.

![Admin Site - Book Add](https://mdn.mozillademos.org/files/13979/admin_book_add.png)

> **Примечание:** А сейчас, хотелось бы, чтобы вы добавили несколько книг, авторов и жанров (например, Фэнтези) в ваше приложение. Удостоверьтесь, что каждый автор и жанр включает пару различных книг (позже, когда мы реализуем представления "list" и "detail", это сделает их более интересными).

После того, когда книги добавлены, для перехода на главную страницу админ-панели кликните на ссылке **Home** в верхней части страницы. Потом кликните на ссылке **Books** для отображения текущего списка книг (или на одной из других ссылок, чтобы увидеть список соответствующей модели). После добавления нескольких книг список может выглядеть наподобие скриншота ниже. Отображается название каждой из книг. Его возвращает метод `__str__()` в модели Book, созданной в предыдущей статье.

![Admin Site - List of book objects](https://mdn.mozillademos.org/files/13935/admin_book_list.png)

Для удаления книги из этого списка выберите чекбокс рядом с ней и действие _delete..._ из выпадающего списка _Action_, а затем нажмите кнопку **Go**. Также можно добавить новую книгу, нажав на кнопку **ADD BOOK**.

Вы можете редактировать книгу, кликнув по ссылке с её названием. Страница редактирования книги, приведённая ниже, практически идентична странице добавления новой книги. Основные отличия - это заголовок страницы (_Change book_) и наличие кнопок **Delete**, **HISTORY** и **VIEW ON SITE.** Последняя присутствует, так как мы определили метод `get_absolute_url()` в нашей модели.

![Admin Site - Book Edit](https://mdn.mozillademos.org/files/13977/admin_book_modify.png)

Теперь перейдите назад на страницу **Home** (используя ссылку _Home_ в навигационной цепочке вверху страницы) и просмотрите списки **Author** и **Genre**. В них уже должно быть несколько элементов, созданных при добавлении новых книг. Если хотите, добавьте ещё.

Однако у вас не будет ни одного экземпляра книги, потому что они не создаются из модели `Book` (хотя можно создать книгу из модели `BookInstance` — такова природа поля `ForeignKey`). Для отображения страницы _Add book instance_ (см. рисунок ниже)\_ _вернитесь на страницу \_Home_ и нажмите кнопку **Add**. Обратите внимание на длинный уникальный Id для идентификации конкретного экземпляра книги в библиотеке.

![Admin Site - BookInstance Add](https://mdn.mozillademos.org/files/13981/admin_bookinstance_add.png)

Создайте несколько экземпляров для каждой из ваших книг. Установите статус _Available (доступен)_ для некоторых экземпляров и _On loan (выдан)_ для остальных. Если статус экземпляра **not** _Available (недоступен)_, то также установите дату возврата (_Due back)_.

Вот и все! Вы изучили как запустить и использовать админ-панель. Также были созданы записи для `Book`, `BookInstance`, `Genre` и `Author`, которые можно будет использовать после создания наших собственных представлений и шаблонов.

## "Продвинутая" конфигурация

Django выполняет неплохую работу по созданию базовой админ-панели используя информацию из зарегистрированных моделей:

- каждая модель имеет список записей, каждая из которых идентифицируется строкой, создаваемой методом `__str__()` модели, и связана с представлением для её редактирования. По умолчанию, в верхней части этого представления находится меню действий, которое может быть использовано для удаления нескольких записей за раз
- Формы для редактирования и добавления записей содержат все поля модели, которые расположены вертикально в порядке их объявления в модели.

Можно настроить интерфейс пользователя для упрощения его использования. Некоторые доступные настройки:

- List views:

  - добавление дополнительных отображаемых полей или информации для каждой записи.
  - добавление фильтров для отбора записей по разным критериям (например, статус выдачи книги).
  - добавление дополнительных вариантов выбора в меню действий и места расположения этого меню на форме.

- Detail views

  - выбор отображаемых полей, их порядка, группирования и т.д.
  - добавление связанных полей к записи (например, возможности добавления и редактирования записей книг при создании записи автора).

В этом разделе рассмотрим некоторые изменения для совершенствования интерфейса пользователя нашей местной библиотеки, а именно: добавление дополнительной информации в списки моделей `Book` и `Author` , а также улучшение расположения элементов соответствующих представлений редактирования. Пользовательский интерфейс моделей `Language` and `Genre` изменять не будем, так как это не даст заметного улучшения, поскольку он содержит только по одному полю!

Полное руководство по всем возможным вариантам настройки админ-панели можно найти в [The Django Admin site](https://docs.djangoproject.com/en/1.10/ref/contrib/admin/) (документация Django).

### Регистрация класса ModelAdmin

Для изменения отображения модели в пользовательском интерфейсе админ-панели, необходимо определить класс [ModelAdmin](https://docs.djangoproject.com/en/dev/ref/contrib/admin/#modeladmin-objects) (он описывает расположение элементов интерфейса, где Model - наименование модели) и зарегистрировать его для использования с этой моделью.

Давайте начнём с модели Author. Откройте файл **admin.py** в каталоге приложения (**/locallibrary/catalog/admin.py**). Закомментируйте исходную регистрацию (используя префикс #) этой модели:

```js
# admin.site.register(Author)
```

Теперь добавьте новый класс `AuthorAdmin` и зарегистрируйте его как показано ниже:

```python
# Define the admin class
class AuthorAdmin(admin.ModelAdmin):
    pass

# Register the admin class with the associated model
admin.site.register(Author, AuthorAdmin)
```

Сейчас мы добавим классы `ModelAdmin` для моделей `Book`, и `BookInstance`. Нам снова нужно закомментировать исходную регистрацию:

```js
#admin.site.register(Book)
#admin.site.register(BookInstance)
```

В этот раз для создания и регистрации новых моделей используем декоратор `@register` (он делает то же самое, что и метод `admin.site.register()`):

```python
# Register the Admin classes for Book using the decorator

@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    pass

# Register the Admin classes for BookInstance using the decorator

@admin.register(BookInstance)
class BookInstanceAdmin(admin.ModelAdmin):
    pass
```

Пока что все наши admin-классы пустые (см. "`pass"`), поэтому ничего не изменится ! Добавим код для задания особенностей интерфейса моделей.

### Настройка отображения списков

Сейчас приложение _LocalLibrary_ отображает всех авторов, используя имя объекта, возвращаемое методом `__str__()` модели. Это приемлемо, когда есть только несколько авторов, но, если их количество значительно, возможны дубликаты. Чтобы различить их или просто отобразить более интересную информацию о каждом авторе, можно использовать [list_display](https://docs.djangoproject.com/en/dev/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_display) (для добавления дополнительных полей).

Замените класс `AuthorAdmin` кодом, приведённым ниже. Названия полей, которые будут отображаться в списке, перечислены в кортеже list_display в требуемом порядке (это те же имена, что и в исходной модели).

```python
class AuthorAdmin(admin.ModelAdmin):
    list_display = ('last_name', 'first_name', 'date_of_birth', 'date_of_death')
```

Перезапустите сайт и перейдите к списку авторов. Указанные поля должны отображаться следующим образом:

![Admin Site - Improved Author List](https://mdn.mozillademos.org/files/14023/admin_improved_author_list.png)

Для нашей модели `Book` добавим отображение полей `author` и `genre`. Поле `author` - это внешний ключ (`ForeignKey` ) связи один к одному, поэтому оно будет представлено значением `__str()__` для связанной записи. Замените класс `BookAdmin` на версию, приведённую ниже.

```python
class BookAdmin(admin.ModelAdmin):
    list_display = ('title', 'author', 'display_genre')
```

К сожалению, мы не можем напрямую поместить поле genre в `list_display`, так как оно является `ManyToManyField` (Django не позволяет это из-за большой "стоимости" доступа к базе данных). Вместо этого мы определим функцию `display_genre` для получения строкового представления информации (вызов этой функции есть в `list_display`, её определение см. ниже).

> **Примечание:** Получение здесь значения поля `genre` возможно не самая хорошая идея вследствие "стоимости" операции базы данных. Мы показываем это, потому что вызов функций в ваших моделях может быть очень полезен по другим причинам, например, для добавления ссылки _Delete_ рядом с каждым пунктом списка.

Добавьте следующий код в вашу модель `Book` (**models.py**). В нем создаётся строка из первых трёх значений поля `genre` (если они существуют) и `short_description`, которое может быть использовано в админ-панели.

```python
    def display_genre(self):
        """
        Creates a string for the Genre. This is required to display genre in Admin.
        """
        return ', '.join([ genre.name for genre in self.genre.all()[:3] ])
    display_genre.short_description = 'Genre'
```

После сохранения модели и обновления админ-панели, перезапустите её и перейдите на страницу списка _Books_. Вы должны увидеть список книг, наподобие приведённого ниже:

![Admin Site - Improved Book List](https://mdn.mozillademos.org/files/14025/admin_improved_book_list.png)

Модель `Genre` (и модель `Language`, если вы её определили) имеет единственное поле. Поэтому нет необходимости создания для них дополнительных моделей с целью отображения дополнительных полей.

> **Примечание:** целесообразно, чтобы в списке модели `BookInstance` отображались хотя бы статус и ожидаемая дата возврата. Мы добавили это в качестве "испытания" в конце этой статьи!

### Добавление фильтров списка

Если в вашем списке есть множество элементов, может быть полезной возможность фильтрации отображаемых пунктов. Это выполняется путём перечисления их в атрибуте `list_filter`. Замените класс `BookInstanceAdmin` на следующий:

```python
class BookInstanceAdmin(admin.ModelAdmin):
    list_filter = ('status', 'due_back')
```

Представление списка теперь будет содержать панель фильтрации справа. Обратите внимание, как выбирать даты и статус для фильтрации:

![Admin Site - BookInstance List Filters](https://mdn.mozillademos.org/files/14037/admin_improved_bookinstance_list_filters.png)

### Формирование макета с подробным представлением

По умолчанию в представлениях деталей отображаются все поля по вертикали в порядке их объявления в модели. Вы можете изменить порядок декларации, какие поля отображаются (или исключены), используются ли разделы для организации информации, отображаются ли поля горизонтально или вертикально, и даже какие виджеты редактирования используются в админ-формах.

> **Примечание:** Модели LocalLibrary относительно просты, поэтому нам не нужно менять макет, но мы всё равно внесём некоторые изменения, просто чтобы показать вам, как это сделать.

#### Управление отображаемыми и вложенными полями

Обновите ваш AuthorAdmin класс, чтобы добавить строку полей, как показано ниже (выделено полужирным шрифтом):

```python
class AuthorAdmin(admin.ModelAdmin):
    list_display = ('last_name', 'first_name', 'date_of_birth', 'date_of_death')
    fields = ['first_name', 'last_name', ('date_of_birth', 'date_of_death')]
```

Атрибут полей перечисляет только те поля, которые должны отображаться в форме, по порядку. Поля отображаются по вертикали по умолчанию, но будут отображаться горизонтально, если вы дополнительно группируете их в кортеже (как показано в полях «date» выше).

Перезагрузите приложение и перейдите к подробному представлению автора - он должен теперь отображаться, как показано ниже:

![Admin Site - Improved Author Detail](https://mdn.mozillademos.org/files/14027/admin_improved_author_detail.png)

> **Примечание:** Так же, вы можете использовать `exclude` атрибут для объявления списка атрибутов, которые будут исключены из формы (все остальные атрибуты в модели, будут отображаться).

#### Разделение на секции/Выделение подробного представления

Вы можете добавлять "разделы" (sections) для группировки связанной информации в модели в форме детализации, используя атрибут [fieldsets](https://docs.djangoproject.com/en/dev/ref/contrib/admin/#django.contrib.admin.ModelAdmin.fieldsets) .

В модели `BookInstance` мы имеем информацию соответствия конкретной книги (т.е. `name`, `imprint`, and `id`) и датой когда она вновь станет доступной (`status`, `due_back`). Мы можем добавить их в разные секции, добавив текст жирным шрифтом в наш `BookInstanceAdmin` класс.

```python
@admin.register(BookInstance)
class BookInstanceAdmin(admin.ModelAdmin):
    list_filter = ('status', 'due_back')

    fieldsets = (
        (None, {
            'fields': ('book','imprint', 'id')
        }),
        ('Availability', {
            'fields': ('status', 'due_back')
        }),
    )
```

Каждая секция имеет свой заголовок (или `None`, если заголовок не нужен) и ассоциированный кортеж полей в словаре - формат сложный для описания, но относительно простой для понимания, если вы посмотрите на фрагмент кода, представленный выше.

Перезапустите сайт и перейдите к списку экземпляров; форма должна отображаться следующим образом:

![Admin Site - Improved BookInstance Detail with sections](https://mdn.mozillademos.org/files/14029/admin_improved_bookinstance_detail_sections.png)

### Встроенное редактирование связанных записей

Иногда бывает полезно иметь возможность добавлять связанные записи одновременно. Например, имеет смысл иметь как информацию о книге, так и информацию о конкретных копиях, которые вы получили на той же странице подробностей. К примеру, вполне логично получить и информацию о книге, и информацию о конкретных копиях, зайдя на страницу детализации.

Вы можете это сделать, объявив [inlines](https://docs.djangoproject.com/en/dev/ref/contrib/admin/#django.contrib.admin.ModelAdmin.inlines), и указав тип [TabularInline](https://docs.djangoproject.com/en/dev/ref/contrib/admin/#django.contrib.admin.TabularInline) (горизонтальное расположение) или [StackedInline](https://docs.djangoproject.com/en/dev/ref/contrib/admin/#django.contrib.admin.StackedInline) (вертикальное расположение, так же как и в модели по умолчанию). Вы можете добавить `BookInstance` информацию в подробное описание `Book` , добавив строки, представленные ниже и располагающиеся рядом с `BookAdmin`:

```python
class BooksInstanceInline(admin.TabularInline):
    model = BookInstance

@admin.register(Book)
class BookAdmin(admin.ModelAdmin):
    list_display = ('title', 'author', 'display_genre')
    inlines = [BooksInstanceInline]
```

Попробуйте перезапустить приложение, а затем взгляните на представление книги — внизу вы должны увидеть экземпляры книги, относящиеся к этой книге:

![Admin Site - Book with Inlines](https://mdn.mozillademos.org/files/14033/admin_improved_book_detail_inlines.png)

В этом случае, всё, что мы сделали - объявили наш встроенный класс tablular, который просто добавляет все поля из _встроенной_ модели. Вы можете указать все виды дополнительной информации для макета, включая отображаемые поля, их порядок, независимо от того, являются ли они только для чтения или нет, и т. д. (См. [TabularInline](https://docs.djangoproject.com/en/dev/ref/contrib/admin/#django.contrib.admin.TabularInline) для получения дополнительной информации).

> **Примечание:** В этой функции есть некоторые неприятные ограничения! На скриншоте выше у нас есть три существующих экземпляра книги, за которыми следуют три поля для новых экземпляров книги (которые очень похожи!). Было бы лучше НЕ иметь лишних экземпляров книг по умолчанию и просто добавить их с помощью ссылки **Add another Book instance** или иметь возможность просто перечислять `BookInstance`s как нечитаемые здесь ссылки. Первый вариант можно сделать, установив `extra` атрибут в 0 в модели `BookInstanceInline`, попробуйте сами.

## Проверьте себя

Мы многое изучили в этом разделе и теперь настало время вам самостоятельно попробовать несколько вещей:

1. Для представления списка `BookInstance` , добавьте код для отображения книги, статуса, даты возврата, и id (вместо значения по умолчанию возвращаемого `__str__()` ).
2. Добавьте встроенный список перечня `Book` в представление списка `Author` , используя тот же самый подход, который мы применили для `Book`/`BookInstance`.

## Заключение

Вот и всё! Теперь вы узнали, как настроить сайт администрирования как в самой простой, так и в улучшенной форме, о создании суперпользователя и о том, как перемещаться по сайту администратора, просматривать, удалять и обновлять записи. По пути вы создали множество книг, экземпляров, жанров и авторов, которые мы сможем перечислить и отобразить, как только мы создадим собственный вид и шаблоны.

## Дополнительные материалы

- [Writing your first Django app, part 2: Introducing the Django Admin](https://docs.djangoproject.com/en/1.10/intro/tutorial02/#introducing-the-django-admin) (Django docs)
- [The Django Admin site](https://docs.djangoproject.com/en/1.10/ref/contrib/admin/) (Django Docs)

{{PreviousMenuNext("Learn/Server-side/Django/Models", "Learn/Server-side/Django/Home_page", "Learn/Server-side/Django")}}