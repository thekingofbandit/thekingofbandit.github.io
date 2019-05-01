# Home

For full documentation visit [thekingofbandit.github.io](https://thekingofbandit.github.io/Documentation).

## Topics

* `update Godaddy DNS` - Update IP Address to GoDaddy Registered Domain.
* `Start Alpha WAS` - Start Alpha Web Aplication Server (WAS).
* `Restart Alpha WAS` - Restart Alpha WAS using batch file.
* `Deploy Dump file` - Restore Dump File.
* `Copy File using robocopy` - Copy file using robocopy.
* `Copy File using xcopy` - Copy file using xcopy.
* `Extract tar.gz using batch file` - Extract tar.gz file using batch file.
* `List file to .txt` - list files in a folder and extract to .txt file.
* `Upload files using DropBox API` - Upload files to DropBox using API.
* `Split CSV files` - Split a CSV into separate files.
* `Create SSH Account` - Installing SSH and create an account.
* `Create Cert` - Create cert identifier.
* `ZNC Config enabled log` - Setup ZNC with logging enabled.
* `Create S3 Bucket` - Setup S3 Bucket.
* `Upload a File in S3 using alpha` - Upload files in S3 Bucket.
* `Login using 3rd Party in Alpha` - HTTPS,  domain like .com required.
* `Upgrade python in ubuntu` - Upgrade python in ubuntu.
* `Change Favicon in mkdocs` - Change favicon in mkdocs.
## Project layout

    mkdocs.yml    		# The configuration file.
    docs/
        index.md  		# The documentation homepage.
        about.md  		# About
        css/
        	extra.css 	#extra css for customise
        js/
        	extra.js 	#extra javascript for customise
    custom_theme
    	img/favicon.ico #custom favicon

## Books
* [linux PDF](https://thekingofbandit.github.io)
* [Guide Front-End 2019](https://thekingofbandit.github.io)
* [Guide Front-End 2018](https://thekingofbandit.github.io)


## Links

* [thekingofbandit.github.io](https://thekingofbandit.github.io)
* [Lower Case Table](https://mariadb.com/kb/en/library/server-system-variables/#lower_case_table_names)
* [ubuntu](https://ubuntu.com)
* [Facebook API KEY](https://thekingofbandit.github.io)
* [Speedtest](https://thunix.net/~amcclure/speedtest/example-customSettings2.html)

## Codes

### Start mkdocs
```
mkdocs serve
```

### Build mkdocs
```
mkdocs build
```

### Create Event
```MYSQL
CREATE EVENT CreateDailyProfile
ON SCHEDULE EVERY 1 DAY
STARTS '2018-10-24 11:45:00.000'
ON COMPLETION PRESERVE
ENABLE
COMMENT 'start 11:45  +- 30 menit'
DO CALL LCCI_DoAll();
```

### Enable Event Scheduler
```MYSQL
SET GLOBAL event_scheduler = ON;
```

### Event Scheduler Status
```MYSQL
SHOW VARIABLES
WHERE VARIABLE_NAME = 'event_scheduler';
```

### Watch Processlist
```MYSQL
mysqladmin -u root -p -i 1 processlist;
```

### Facebook API Key Secret
```
CzMUqcL/HDzqwOPoXTpCHIrmjiwm+UxPZVel5pZppXrVhMPWN44+e9BUOT7u6a4OYmDKkPDuoc2wjykpKEvYOsa9rTRJ+xQWBe9Ba3BkfQR1usZoJCDA3HBxv7osTbXzahVnjnHCePA0x/C1PuVGbsJqvxt3L2zat/rMkEjGv6ROa7nhr6YZ+F2b9DwUqr3JiR6HtMajSHBZEISnqzN41C28Rskc33bZ7ZRA+KpJDWtCf1rKxHCuaBMoa+v0iWrrhnnftV1Dn36fyaGTKa5mQATU3FTP09LOw0igAY8QiuLlNoTGGvndvd5CuuMu75+YwU8zGfqpYLnqpa0sBNoWh9b89OC9WdIqB34PlY5nLNNQYBpJqt7iC/0bNKo5j1voJSMWLQ7YZcxVXFo6kDT0Ghj7rC8dGgGdzf5c+FAGYt3AFwmHTKDJgTompDsglfNsF5bIUgBcZGhzA53MyicBBIwcIsRfTUfpO+vPPkzQvpU=
```

### Google API Key Secret
```
CzMUqcL/HDzH9RDac/XPHG3uv2aoZjHAt+vVyRPOXRkojG8QRR25X9VaCuHxLhdazq4vvxtvAuCrpy+Cc10d5guV5HNiily3be4Qr+wkU3ZVi90yQdfILDtiif/RxhIFvsYOkRH/J+1nT4QtxB9uFv6jgACPF43BhVVyYSEgzOmgCACDh4b7MxlxRO9BtXNBo+bNNr3vMAUz6EzbqulGH3nXDFEn2vvyccznrXkqMRxvqSZEvXbAyjmzF2h+nJZD26suCuYnAU3WoQkyAF2MsAYidyxucpvHQVV4bcESs3RrSKzctYpDNHXkbtusNFTt1eR2INj0Ek6LMZL/T2avOnwKWmeRyEIuoDrECBJDBBxfzFPRxMgPwjWxeQJ3Hmu/QMkcOS58HMp6DTpMyvE3M6S1Xj4d2JbEGLQAKCZjlQ9CsZomHIl0/NCcwalZOvT6HBcmxGG5qXq8FM+qJtDjLc47Tu/qAhdx4P6ZXC2UeXTCfzZIFUnzWktmxrNyPzvq/3LDCz1h2uoEu6X4ndqWKZpQ5STl9wVfeBc0+G0E9DU=
```

### LinkedIn API Key Secret
```
CzMUqcL/HDzKB9mrRxLMk29OBQTIHGRW8tHBNHEgTiIL9geIU5vCsQCdfhXka3YBKYhSnLQBPS79eRjE2oiE10h+u7IJPpxs4naBZq2WvWGYjVzGeiq6uBz+iKnUNugarDUS48xWykXZKNGRckd/8BrlTi0TYCdqhrD05v8vH/sPlIrckw64/nZjYOp6+JDMFrd5VUzTKq59DEKQoLk0gAA0h8kkrsgptxdsuV1gRKWIH+2fl/msaKCmj5WwgvrL71lj1Q3RrjP6Bl4dTIBkNFCSDs+jfvGIbSnVrLa++SiNWsAXRa+A/bzzhIl/F+Lm11XFRTn2cddTAz1Fpqsf/5AFFJor48mTNa6z/Fg6wzO3Nynu6rO6Et2FNw9tx5sN5XWYtz+16Dxoyfo5mr0/s7mc/dUJh070cu2S/QWKa6r2zoqtjfu+HIyDxDQZ4Sol1zNVPAIBY2u4euXFs/NkBYwKrLJlrVes
```

### Twitter API Key Secret 
```
CzMUqcL/HDyGNpl3Zmfj35fmKuEud+7dK1X9i6P5gj3QLPkJ9dTY1/TShS2NaNN++7K/7HXl9Ca9m2/gYjG90fyQoh+fKJAJ/Vk8+39U27dkvXSHvtx5O0aUBWOQPOVi/r0S8EPV9y5D3+kilLT2Q0yFZzOwZGRjkrvpuhW75hduacdI7BAjb6ifpR6j99yHz/iWvxxt0g+1ZaIIW/Izey602l/gBSvKzBY86ZzTxH1YMg+czZl0XL4DHmbUa9Tzv/MSifEzNX3SDZmnE8qCFIKgxGr0B7tSKMmJA9HESBsIlCIWS/NxL9AeThVT0itLc10OkrGb3+CsbPMXfhVbl3U41yYiq5DK0DqrQMQm4T9t0ccSP/dtN6pgd4njbaa/zEE3uY+cDP2nvNbm2vkgOiWwv8lWgonPEXHKxA4HOAmjCz+iMHRHSNJj5SToywo58PT6rWGwabvmBDq+ocObEa1yfYycau4AanroefIYRKp3UvMJajieT/f+XDGbam7uILYilszV1N3hKk8WurKqS9gSWLvMg/mG
```

### Method Using API
```
https://console.developers.google.com/cloud-resource-manager?pli=1
Here is your client ID
1019936097335-psrtmvk7scs2osg3i4skomgjlu6n35l5.apps.googleusercontent.com
Here is your client secret
HR4nyIcdO0F72-vkTmhL5Hzv

facebook
App ID
151395095276478
App Secret
e703b9281f35e18b6be33f005276d023


Test UsersDeleteAdd
    Name    User ID Email   
    Emma Alcdhbagbbfgg Bharambesky  100034821722677 pyibmewurk_1552386389@tfbnw.net
    
    
    
<script>
  window.fbAsyncInit = function() {
    FB.init({
      appId      : '151395095276478',
      cookie     : true,
      xfbml      : true,
      version    : 'v3.2'
    });
      
    FB.AppEvents.logPageView();   
      
  };

  (function(d, s, id){
     var js, fjs = d.getElementsByTagName(s)[0];
     if (d.getElementById(id)) {return;}
     js = d.createElement(s); js.id = id;
     js.src = "https://connect.facebook.net/en_US/sdk.js";
     fjs.parentNode.insertBefore(js, fjs);
   }(document, 'script', 'facebook-jssdk'));
</script>



FB.getLoginStatus(function(response) {
    statusChangeCallback(response);
});


{
    status: 'connected',
    authResponse: {
        accessToken: '...',
        expiresIn:'...',
        signedRequest:'...',
        userID:'...'
    }
}



function checkLoginState() {
  FB.getLoginStatus(function(response) {
    statusChangeCallback(response);
  });
}











Application name: salesforce
Client ID: 86qn9b1o6ufhhd
Created: 3/12/2019
Company: Data Driven Systems
Description:
Salesforce Built in ALpha
App logo:
SDK settings

JavaScript
Valid SDK domains:
http://www.techshelf.co.uk/
Client ID:
86qn9b1o6ufhhd
Client Secret:
BfAo6cEhIJo0pJX2


POST /oauth/v2/accessToken HTTP/1.1
Host: www.linkedin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&code=987654321&redirect_uri=https%3A%2F%2Fwww.myapp.com%2Fauth%2Flinkedin&client_id=123456789&client_secret=shhdonottell


https://docs.microsoft.com/en-gb/linkedin/shared/authentication/authorization-code-flow?context=linkedin/consumer/context
```

### RetroPie ROM
```
PACK FULL SET ROM EU / FR (MikOzzZ et Ano Nyme)


Retrogaming " Recalbox, Retropie, Attract Mode, Batocera, Happi, Hyperspin", Raspberry pi, Odroid, console Oldgen et Nextgen, musique, ciné, Série, BD etc...... 

> Fullset EU/Fr Gameboy
https://mega.nz/#!48RVDBKD!P0AUsB_SQLTQTadnC43nc8MIMkDk73dXATw6rzh0TzU

> Fullset Eu/Fr Gameboy Color
https://mega.nz/#!k9wkiK7C!fMgA6Q1Lh-CVErYK8mbJy5dWW-ZtdRb39DTQg8hnm9c

> Fullset Eu/Fr Gameboy Advance
https://mega.nz/#!BxZWVKiR!91b3_Qd6pJHgdoHcbj-m1UUlX37tOnf45rWBZazj9xQ


> Fullset Eu/Fr Super Nintendo
https://mega.nz/#!h0AD3RYa!73GkI_caZN9XHCPQjIZ3mqZ8fxf0KbRSi54jLznHBes


> Fullset Eu/Fr Nes
https://mega.nz/#!8h4A0Tyb!Ry2Xm8i6GQkuBjStGwRulrYsbJQ67XkFCTrdnYabs_Q


> Fullset Nintendo Virtual Boy
https://mega.nz/#!MsIkADLI!QXoeGEuy7t1Blj7SgH4nWR76B28fbL5On3KJvaVFC1E


> Fullset Eu/Fr Sega GameGear
https://mega.nz/#!BlJxRT7Q!QsNi3OJF-e_NaorgfrWFugJ2HQeYV51mpJ5EnAIuSjk


> Fullset Eu/Fr Sega Master System
https://mega.nz/#!4kZ2SLyQ!uqHIp152d5X2o-qb5pQoVTGXWRUP6eaHukfFTnH_saY


> Fullset Eu/Fr Sega Mega Drive
https://mega.nz/#!xlgjHRLK!WpzJxua3FpRIFvqTt_beeXNZXXNoEpAZytaEXBK80Jk


> Fullset Eu/fr Sega 32X
https://mega.nz/#!t05VBSKC!faMZ3daYX-5I3kT9mE535nIqoRBkzh1xxLmurt6SxMs


> Fullset SG-1000
https://mega.nz/#!18hQQaAa!p-UPOL72H-dT6jxLto7Lt9RCz7wibCDMdAMrCqgnjgk


> Fullset Atari 7800 PAL + BIOS
https://mega.nz/#!B5hDwK5C!6uR06MPw17LskUQnN_V-ex9WLi-GKuH7v4f4tNZTu7M


> Fullset Atari 7800 US
https://mega.nz/#!VxBA2YCa!LDsF1Km8K8BxYH0dwefjT7hRUgCPD6yA1fSka8CDM4o


> Fullset Atari Lynx
https://mega.nz/#!ptoWSTaa!fOssjg8lpP3zV1PPhkKeFESVAj2LyAphi-sXU9koTQM


> Fullset Vectrex
https://mega.nz/#!wkhBUR5b!jxNjy50ImGOEbCgnQMVmy6bprvy8xtNzC42S-Ozeo_U


> Fullset PC-Engine Super GrafX
https://mega.nz/#!FhhwzSiB!aSYq88u5Pu1-WGIc-6wbshsAtNtgY7PPWkdD43NvqrE


> Fullset WonderSwan
https://mega.nz/#!0lA2mCwZ!PgIU0Yt4X2MkHPkPyUHvBIRWVKST1YTogTHUt8tTRfQ


> Fullset WonderSwan Color
https://mega.nz/#!4hhhgIYZ!2LjGZ0ejUpW73aM3vFDDvATlIWYPQyIhgCmQp4OC0nM


> Fullset ColecoVision
https://mega.nz/#!EoBVQawA!_aAYA6V4IblO2AcObTpZKaGXdj_1uykBgKqSjfPdUBg


> Pack de Scrap fba_libretro RECALBOX (17.12.02 - 18.02.09) pour le pack de Mikoz You 
https://mega.nz/#!kkpEVS4Q!mGQDJGuPUoeIiZkMNiTPy5WiKXKxN4URfGz11UITfrk


> Pack de Scrap NeoGeo RECALBOX (17.12.02 - 18.02.09) pour le pack de Mikoz You
https://mega.nz/#!9tx1WDpB!gqGRmDC4ovUP8i_isdfP2J9cAlq_ilx6QVu7KQrVRM8


-> Pack de Scrap MAME 0.139 (2010) RECALBOX (17.12.02 - 18.02.09) pour le pack de Mikoz You
https://mega.nz/#!Z4BV1CYB!bo5l7HQc9ByJE4FusKjjXoetkAa9TIl8Tlq1LolfKgY
```
