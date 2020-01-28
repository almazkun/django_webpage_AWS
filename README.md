# Instruction on creating new Django webpage on AWS
In the end you will have a working webpage deployed on AWS.

**Requirements** Python 3 and Django 3 is required. 

## 1. Buy domain. 
* Go to registrar [www.porkbun.com](https://porkbun.com) and purchase a domain name. I have bought:
    [akun.dev](https://akun.dev)

## 2. New free email for your domain. 
This is optional step, do this only if you want to have a email address like: `john@yourdomain.com`. Otherwise go toi the step 3: Create Django website.

1. Register on [yandex.com](https://yandex.com/), login with your new yandex account and go to the [connect.yandex.com](https://connect.yandex.com).
2. The go to the [admin section](https://connect.yandex.com/portal/admin)
3. Add `New organization`. It should be your newly purchased `domain.com`. in my case it is `akun.dev`.
4. Confirm an ownership over the domain. https://connect.yandex.com/portal/services/webmaster/resources/`domain.com`. In my case `https://connect.yandex.com/portal/services/webmaster/resources/akun.dev`. Choose `DNS record` confirmation (`DNS` tab). It will ask you to add following TXT-record (something like: `yandex-verification: 5544abs2a143db9f`) to your DNS records. Copy your conformation record.
5. Go to the https://porkbun.com/account/domains, find your domain, hit `Details` button. Find `DNS RECORD` and pres `Edit`. Fill out following sections:

    **Type:** `TXT - Text record`. **Answer:** `yandex-verification: 5544abs2a143db9f`
    
    **Note** Leave everything else untouched. 

    Hit `Add` button.
    New record should be added at the `Current Records` table. 

6. Return to the https://connect.yandex.com/portal/services/webmaster/resources/`domain.com`, Under the `DNS` tab hit the `Check` button. Wait for a while in should redirect you and say that `Domain confirmed`.
7. Now we need to delete old mail DNS records on the https://porkbun.com/account/domains, find your domain, hit `Details` button. Find `DNS RECORD` and pres `Edit`. Basically, you need to delete all `MX`records and `TXT` record starting with `v=spf1`, because we will transfer all email handlings to the [Yandex](connect.yandex.com). 

    Delete at least following records:

    |Type|Host|Answer|Priority|
    |---|---|---|---|
    |CNAME|mail.domain.com|domain.com||
    |MX|domain.com|fwd1.porkbun.com|1|
    |MX|domain.com|fwd1.porkbun.com|1|
    |TXT|domain.com|v=spf1 mx ~all||

8. After domain confirmation it will provide you with new DNS records from Ynadex. We will need to copy all these records to the our porkbun.com DNS records. Like we did in step #7. 
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

9. Now go to the https://connect.yandex.com/portal/admin. Here we will add a new email address. 
    * Click on the `All employees` tab on the left side menu. 
    * Click on the `+ Add` on the bottom of the page, choose `Add a person`.
    * Fill out the popup form, mandatory felids are:
    
    |New Employee||
    |---|---|
    |Last Name|Kunpeissov|
    |Name|Almaz|
    |Login|hello *(it means email address will be `hello@akun.dev`)*|
    |Password|VERY_STRONG_PASSWORD|
    |One more time|VERY_STRONG_PASSWORD|

    Hit `Add` button. 

10. Now we need to check if your new email is working. Go to the [yandex.com](https://yandex.com/). Login there using your newly created email address.

    * Login: `hello@akun.dev`
    * Password: `VERY_STRONG_PASSWORD`

    Complete the registration and you will be forwarded to the `inbox` page of your new email address. Compose a random email to your self and some other email address to check if it is working. It should be working. If you can not receive any email, most probably you should look at the DNS configuration again. Delete old records of `Porkbun` and add all new records of `Yandex`.

## 3. Create Django website.
We will create Django portfolio web site.
1. Create a new folder and navigate into it:
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
8. In this section we will create front-end of a website. We will use [Bootstrap 4](https://getbootstrap.com/) framework to help us make our webpage beautiful. 

    1. Create `templates` folder and `home.html` file:
    ```powershell
    New-Item -Path . -Name "templates" -ItemType "directory"
    New-Item -Path ./templates/ -Name "home.html" -ItemType "file"
    ```

    2. Open `home.html` file and add following code:
    ```html
    <!DOCTYPE html>
    <html lang="en">
        <head>
            <!-- Required meta tags -->
            <meta charset="utf-8">
            <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
            <title>My New and Awesome Django website on AWS</title>
            <!-- Stylesheets -->
            <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
        </head>
        <body>

            <div class="bg-primary navbar-dark text-white">
                <div class="container">
                    <nav class="navbar px-0 navbar-expand-lg navbar-dark">
                        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNavAltMarkup" aria-controls="navbarNavAltMarkup" aria-expanded="false" aria-label="Toggle navigation">
                            <span class="navbar-toggler-icon"></span>
                        </button>
                        <div class="collapse navbar-collapse" id="navbarNavAltMarkup">
                            <div class="navbar-nav">
                                <a href="#" class="pl-md-0 p-3 text-light">My Website</a>
                            </div>
                        </div>
                    </nav>
                </div>
            </div>

            <div class="jumbotron bg-light mb-0 radius-0">
                <div class="container">
                    <div class="row">
                        <div class="col-xl-8">
                            <h1 class="display-1 display-fix">Awesome</h1>
                            <span class="lead">My New and Awesome Django website on AWS</span>
                            <div class="mt-4">
                                <a href="#!"
                                    class="btn btn-wide btn-primary btn-lg my-2 mr-2">
                                    <span>A Button</span>
                                </a>
                                <a href="#!"
                                    class="btn btn-wide btn-outline-dark btn-lg my-2">
                                    <span>Another button</span>
                                </a>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <div class="container py-5">
                <h1>Thanks</h1>
                <p>Thank you for downloading this theme. If you have trouble or find a bug, please open an issue on GitHub:<br>
                    <a href="https://github.com/HackerThemes/theme-machine">https://github.com/HackerThemes/theme-machine</a>
                </p>
            </div>

        </body>
    </html>
    ```
    3. Let's check how is out website is doing:
    ```powershell
    python manage.py runserver
    ```
    Now open a browser and open following address:
    ```
    127.0.0.1:8000
    ```
    You should be able to see your website.
