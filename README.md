# Translations @ Django projects with i18n
<br>

#### GitHub:
https://github.com/BergerMarcin/TranslateDjango.git
<br><br>

#### Documentation (from the best):
- https://docs.djangoproject.com/en/dev/topics/i18n/translation/ 
- https://docs.djangoproject.com/en/3.0/topics/i18n/
- https://matix.io/django-i18n-translation-cheatsheet/
- https://medium.com/@nolanphillips/a-short-intro-to-translating-your-site-with-django-1-8-343ea839c89b 
<br><br>

#### Notes:
 - `messages` (text to be translated) are specialy marked texts from the script (python files and templates) + warning 
 messages + error messages
 - `locale` (or `locales`) - recommended folders name for translation files
 - files `*.po` - message files (with texts to be translated)
 - files `*.mo` - compiled message files (to be used by Django; might be read as txt files)
<br><br>

# Application procedure
1. **Set Django project from terminal** with (& some settings in settings.py & database creation):
    - **`django-admin startproject <project_name> .`**    # important whitespace and dot after project_name
    - **`virtualenv -p python3 venv`**    # venv folder should be subfolder of the folder where manage.py is located
    - **`source venv/bin/activate`**
    - **`pip3 install django==2.2.7`**
    - **`pip3 install psycopg2-binary==2.8.3`**   # controller in case applied PostgreSQL
    - **`pip3 install pytz==2019.3`**    # for time-zones controll
    - **`pip3 install sqlparse==0.3.0`**
    - **`django-admin version`**     # to check django installation success (by checking it's version)
    - **`python manage.py runserver`**     # to check django start (should start with some 17 minor warnings)
    - stop server with Ctrl+C
    - set DB in settings.py
    - create database acc. settings.py's database settings
    - **`python manage.py migrate`**
    - **`python manage.py startapp <app_name>`**
    - **`python manage.py runserver`**     # to check django start (rocket at website)
    - stop server with Ctrl+C
    - **`python manage.py makemigrations`**
    - **`python manage.py migrate`**
<br><br>
2. **Install i18n** (module django-i18n is installed with django by default). Please in terminal** apply:
    - `pip3 install i18n`
<br><br>
3. **settings.py**:
```
...
import os
from django.utils.translation import ugettext_lazy as _
…
# Build paths inside the project like this: os.path.join(BASE_DIR, ...)
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
...
# Application definition
INSTALLED_APPS = [
    ...
    'i18n',
    ...
]
...
MIDDLEWARE = [
    ...
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    ...
    'django.middleware.locale.LocaleMiddleware',  # specific for i18N
    ...
]
...
# Translation (part of Internationalization)
# Provide a lists of languages which your site supports.
# https://docs.djangoproject.com/en/dev/topics/i18n/translation/#how-django-discovers-language-preference
LANGUAGES = (
    ('en', _('English')),
    ('pl', _('Polish')),
)
# Set the default language (recommended English)
# https://docs.djangoproject.com/en/3.0/topics/i18n/
LANGUAGE_CODE = 'en'
TIME_ZONE = 'CET'
USE_I18N = True
USE_L10N = True
USE_TZ = True
# A list of directories where Django looks for translation files
# https://docs.djangoproject.com/en/3.0/ref/settings/#locale-paths
LOCALE_PATHS = (
    os.path.join(BASE_DIR, 'locale'),    # Optional, default path './locales' folder for app's translation files
)
```
<br>4. **Create messages** (mark your texts to be collected and translated): 
<br>
 
https://docs.djangoproject.com/en/dev/topics/i18n/translation/#internationalization-in-python-code
  - @ **python** files with **text**:
```
       from django.utils.translation import ugettext_lazy as _
       print(_('Hello World!'))
```
  - @ python files with **text & variables**:
```
       from django.utils.translation import ugettext_lazy as _
       output = _('Today is %(month)s %(day)s.') % {'month': m, 'day': d}
```
  - @ python files with **text & variables with pluralization**:
```
       from django.utils.translation import ngettext
       page = ngettext(
             'there is %(count)d object',
             'there are %(count)d objects',
       count) % {
             'count': count,
       }
```
<br>

https://docs.djangoproject.com/en/dev/topics/i18n/translation/#internationalization-in-template-code
   - @ **template** files with **text**:
```
       {% load i18n %}
       <h2>{% trans 'Hello World!' %}</h2>
```
  - @ template files with **variables alone** (text & variables should be separated except blocktranslate):
```
       {% load i18n %}
       {% translate "This is the title" as the_title %}
       <title>{{ the_title }}</title>
       <meta name="description" content="{{ the_title }}">
```
   - @ template files with **text & variables** applied **blocktranslate** *(to mix text & variables)*:
```
       {% load i18n %}
       {% blocktranslate %}This string will have {{ value }} inside.{% endblocktranslate %}
```
  - @ template files with **variables with pluralization** only with **blocktranslate**:
```
       {% load i18n %}
       {% blocktranslate count counter=list|length %}
       There is only one {{ name }} object.
       {% plural %}
       There are {{ counter }} {{ name }} objects.
       {% endblocktranslate %}
```
<br>5. **Translate messages** (collect and translate marked texts)

  - **django's automized collection marked texts** from all project files into files `*.po` (called message files). 
  Message files are located @ `locale/<language>/LC_MESSAGES` subfolders of project and of each app (or single app*). 
  Please in terminal* apply one of:
    <br>**`django-admin makemessages -l fr`**   # in case only 1 language to be collected
    <br>**`django-admin makemessages -a`**   # in case all languages
    <br>**`django-admin makemessages`**    #after first collection 
    >** - the script should be run from one of two places: the root directory of your Django project (the one that 
    contains manage.py)  OR  the root directory of one of your Django apps

  - **manual translation** in `*.po` files: text marked with key `msgid` should be translated by YOU** @ key `msgstr` 
    <br>What is nice: Once translated messages (texts) are kept at message files `*.po` after repeated/updated 
    collection of messages (texts to be translated)
    > ** - if you created translation script for *.po files please share :) 

  - **django's automized compile message** (translate to binary file `*.mo` located @ `locale/<language>/LC_MESSAGES` 
  subfolders of project and of each app (or single app*). This file might be read as txt file. Please in terminal** apply:
    <br>**`django-admin compilemessages`**
    > ** - the script should be run from one of two places: the root directory of your Django project (the one that 
    contains manage.py)  OR  the root directory of one of your Django apps.

<br>

*Folder structure after collection, translation and compilation:*
```
myproject/
    locale/		
        fr/		
            LC_MESSAGES/		
                django.mo		 - compiled (for django viewer) collection with messages - in that case all error messages
                django.po		 - collection with messages (marked texts to be translated)		
    myproject/		
        templates/		
            login.html		
        locale/		
            fr/		
                LC_MESSAGES/		
                    django.mo		 - compiled (for django viewer) collection with messages (marked texts to be translated)
                    django.po		 - collection with messages (marked texts to be translated)
    myapp/		
        templates/		
            sale.html		
        locale/		
            fr/		
                LC_MESSAGES/		
                    django.mo		 - compiled (for django viewer) collection with messages (marked texts to be translated)
                    django.po		 - collection with messages (marked texts to be translated)
```
<br>

## Detect language
 - phyton files (https://docs.djangoproject.com/en/dev/topics/i18n/translation/#localized-names-of-languages):
```
    from django.utils.translation import activate, get_language_info
    activate('fr')
    li = get_language_info('de')
    print(li['name'], li['name_local'], li['name_translated'], li['bidi'])   # German Deutsch Allemand False
```
 - template files:
<br>https://docs.djangoproject.com/en/dev/topics/i18n/translation/#miscellaneous (see: example HTML template code)
<br><br>

## Switch language
 - python app (especially in View/ModelView/generic View):
```
    language = 'en'     # (or other like: 'pl', 'fr', 'de')
    translation.activate(language)
    request.session[translation.LANGUAGE_SESSION_KEY] = language
```
  - templates files:
     - https://docs.djangoproject.com/en/dev/topics/i18n/translation/#switching-language-in-templates  
     - https://docs.djangoproject.com/en/dev/topics/i18n/translation/#other-tags
<br><br>

## TIPs:
1. In case problems and errors close browser (to reset session, so language choice)
2. Keep English (en) as origin/basic project language
