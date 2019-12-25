# Instruction on creating new Djnago webpage on AWS
In the end you will have a working webpage deployed on AWS. 

## 1. Buy domain. 
* Go to registrar [www.porkbun.com](https://porkbun.com) and purchase a domain name. I have bought:
    akun.dev
# 

## 2. Domain parking and free email. 
1. Register on [yandex.com](https://yandex.com/), login with your new yandex account and go to the [connect.yandex.com](https://connect.yandex.com).
2. The go to the [admin section](https://connect.yandex.com/portal/admin)
3. Add `New organization`. It should be your newly purchased `domain.com`. in my case it is `akun.dev`.
4. Confirm an ownership over the domain. https://connect.yandex.com/portal/services/webmaster/resources/`domain.com`. In my case `https://connect.yandex.com/portal/services/webmaster/resources/akun.dev`.
5. Choose DNS Domain confirmed. It will ask you to add following TXT-record (something like: `yandex-verification: 5544abs2a143db9f`) to your DNS records. Copy your conformation record.
6. Go to the https://porkbun.com/account/domains, find your domain, hit `Details` button. Find `DNS RECORD` and pres `Edit`. 
7. Fill out following sections:

    **Type:** `TXT - Text record`. **Answer:** `yandex-verification: 5544abs2a143db9f`
    
    **Note** Leave everything else untouched. 

    Hit `Add` button.
    New record should be added at the `Current Records` table. 

8. Return to the https://connect.yandex.com/portal/services/webmaster/resources/`domain.com`, Under the `DNS` tab hit the `Check` button. Wait for a while in should redirect you and say that `Domain confirmed`.
9. After domain confirmation it will provide you with new DNS records. We will need to copy all these records to the our porkbun.com DNS records. Like we did in step #7. 
Table will look similar to this:

    |Host | Record Type | Record Value | Priority |
    |---|---|---|---|
    |mail|CNAME|domain.mail.yandex.net.||
    |@|MX|mx.yandex.net.|10|
    |@|TXT|v=spf1 redirect=_spf.yandex.net||
    |mail._domainkey|TXT|v=DKIM1; k=rsa; t=s; p=VERY_LONG_RANDOM_SYMBOLS||

    Again, go to the https://porkbun.com/account/domains, find your domain, hit `Details` button. Find `DNS RECORD` and press `Edit` and add following 4 new records:
    1. **Type:** `CNAME - Canonical name record`, **Host:** `mail`, **Answer:** `domain.mail.yandex.net.`
    2. **Type:** `MX - Mail exchange record`, **Answer**: `mx.yandex.net.`, **Priority:** `10`
    3. **Type:** `TXT - Text record`, **Answer**: `v=spf1 redirect=_spf.yandex.net`
    4. **Type:** `TXT - Text record`, **Host:** `mail._domainkey`, **Answer**: `v=DKIM1; k=rsa; t=s; p=VERY_LONG_RANDOM_SYMBOLS`
    
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

    Complete the registration and you will forwarded to the `inbox` of your new e-mail address. Compose a random e-mail to your self and some other e-mail address to check if it is working. It should be working. 

*

 /89