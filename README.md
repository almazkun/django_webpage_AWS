# Instruction on creating new Django webpage on AWS
In the end you will have a working webpage deployed on AWS. 

## 1. Buy domain. 
* Go to registrar [www.porkbun.com](https://porkbun.com) and purchase a domain name. I have bought:
    akun.dev

## 2. New free e-mail for your domain. 
1. Register on [yandex.com](https://yandex.com/), login with your new yandex account and go to the [connect.yandex.com](https://connect.yandex.com).
2. The go to the [admin section](https://connect.yandex.com/portal/admin)
3. Add `New organization`. It should be your newly purchased `domain.com`. in my case it is `akun.dev`.
4. Confirm an ownership over the domain. https://connect.yandex.com/portal/services/webmaster/resources/`domain.com`. In my case `https://connect.yandex.com/portal/services/webmaster/resources/akun.dev`.
5. Choose DNS Domain confirmed. It will ask you to add following TXT-record (something like: `yandex-verification: 5544abs2a143db9f`) to your DNS records. Copy your conformation record.
6. Go to the https://porkbun.com/account/domains, find your domain, hit `Details` button. Find `DNS RECORD` and pres `Edit`. Fill out following sections:

    **Type:** `TXT - Text record`. **Answer:** `yandex-verification: 5544abs2a143db9f`
    
    **Note** Leave everything else untouched. 

    Hit `Add` button.
    New record should be added at the `Current Records` table. 

7. Return to the https://connect.yandex.com/portal/services/webmaster/resources/`domain.com`, Under the `DNS` tab hit the `Check` button. Wait for a while in should redirect you and say that `Domain confirmed`.
8. Now we need to delete old mail DNS records on the https://porkbun.com/account/domains, find your domain, hit `Details` button. Find `DNS RECORD` and pres `Edit`. Basically, you need to delete all `MX`records and `TXT` record starting with `v=spf1`, because we will transfer all email handlings to the Yandex. 

    Delete at least following records:

    |Type|Host|Answer|Priority|
    |---|---|---|---|
    |CNAME|mail.domain.com|domain.com||
    |MX|domain.com|fwd1.porkbun.com|1|
    |MX|domain.com|fwd1.porkbun.com|1|
    |TXT|domain.com|v=spf1 mx ~all||

9. After domain confirmation it will provide you with new DNS records from Ynadex. We will need to copy all these records to the our porkbun.com DNS records. Like we did in step #7. 
Table will look similar to this:

    |Host | Record Type | Record Value | Priority |
    |---|---|---|---|
    |mail|CNAME|domain.mail.yandex.net.||
    |@|MX|mx.yandex.net.|10|
    |@|TXT|v=spf1 redirect=_spf.yandex.net||
    |mail._domainkey|TXT|v=DKIM1; k=rsa; t=s; p=VERY_LONG_RANDOM_SYMBOLS||

    Again, go to the https://porkbun.com/account/domains, find your domain, hit `Details` button. Find `DNS RECORD` and press `Edit` and add following 4 new records:

    |Type|Host|Answer|Priority|
    |---|---|---|---|
    |CNAME - Canonical name record|mail|domain.mail.yandex.net.||
    |MX - Mail exchange record||mx.yandex.net.|10|
    |TXT - Text record||v=spf1 redirect=_spf.yandex.net||
    |TXT - Text record|mail._domainkey|v=DKIM1; k=rsa; t=s; p=LONG_STRING_OF_RANDOM_SYMBOLS||

10. Now go to the https://connect.yandex.com/portal/admin. Here we will add a new e-mail address. 
    * Click on the `All employees` tab on the left side menu. 
    * Click on the `+ Add` on the bottom of the page, choose `Add a person`.
    * Fill out the popup form, mandatory felids are:
    
    |New Employee||
    |---|---|
    |Last Name|Kunpeissov|
    |Name|Almaz|
    |Login|hello *(it means e-mail address will be `hello@akun.dev`)*|
    |Password|VERY_STRONG_PASSWORD|
    |One more time|VERY_STRONG_PASSWORD|

    Hit `Add` button. 

11. Now we need to check if your new e-mail is working. Go to the [yandex.com](https://yandex.com/). Login there using your newly created e-mail address.

    * Login: `hello@akun.dev`
    * Password: `VERY_STRONG_PASSWORD`

    Complete the registration and you will be forwarded to the `inbox` page of your new e-mail address. Compose a random e-mail to your self and some other e-mail address to check if it is working. It should be working. If you can not receive any email, most probably you should look at the DNS configuration again. Delete old records of `Porkbun` and add all new records of `Yandex`.

## 3. Create Django website.
We will create Django portfolio web site.
1. Create a new folder, something like:
```powershell
New-Item -Path . -Name "akundotdev" -ItemType "directory"
cd akundotdev
```
2. Install Django, create new Project and start App. 
```powershell
pipenv install django==3.0.*
pipenv shell
django-admin startproject akundev_project .
python manage.py migrate
python manage.py startapp portfolio
```
3. Add new app to `settings.py`:
```python
INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    "portfolio.apps.PortfolioConfig" # add this line
]

TEMPLATES = [
        ...
        'DIRS': [os.path.join(BASE_DIR, 'templates')], # add this line
        ...
]
```
4. Now open `/portfolio/views.py` and add following code:
```python
from django.views.generic import TemplateView


class HomePageView(TemplateView):
    template_name = "home.html"
```
5. Create `/portfolio/urls.py`:
```powershell
New-Item -Path . -Name "urls.py" -ItemType "file"
```
6. Add following code to `/portfolio/urls.py`:
```python
from django.urls import path

from .views import HomePageView


urlpatterns = [
    path("", HomePageView.as_view(), name="home")
]
```
7. Update `/akundev_project/urls.py`:
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path("admin/", admin.site.urls),
    path("", include("portfolio.urls")) # add this line
]
```
8. Templates. Create following folders and files:
```powershell
New-Item -Path . -Name "templates" -ItemType "directory"
New-Item -Path ./templates/ -Name "layout" -ItemType "directory"
New-Item -Path ./templates/ -Name "base.html" -ItemType "file"
New-Item -Path ./templates/ -Name "home.html" -ItemType "file"
New-Item -Path ./templates/layout/ -Name "nav.html" -ItemType "file"
New-Item -Path ./templates/layout/ -Name "footer.html" -ItemType "file"
```
