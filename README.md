# HPWARRANTY
External program to check hp warranty.
Created by: Jakub Mierzejewski
## Problem solving

For today - 21.05.2020, there is no avaiable HP API for warranty check. Only way to check it is HP website - https://support.hp.com/us-en/checkwarranty/multipleproducts/

But, looking deeper into this website I have found really interesting POST request.
##### POST multiWarranty
URL : https://support.hp.com/hp-pps-services/os/multiWarranty
With only one param - ssid, formated like: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

With JSON string in request body:
```
{"gRecaptchaResponse":"","obligationServiceRequests":[
{"serialNumber":"XXXXXXXX","isoCountryCde":"US","lc":"EN","cc":"US","modelNumber":null}
]}
```

With JSON string in response witch is really long but there was information about warranty.
Response structure only with warranty end date.
```
    {
        "productWarrantyDetailsVO":[
          {"serialNumber":"XXXXXXXX", "warrantyResultList":[
              {"obligationEndDate":"January 1, 2024"},{"obligationEndDate":"January 1, 2022"}
          ]}
        ]
    }
```
So maybe there is a way to check warranty somehow using this POST request.

First problem is ssid, it is generated when you access HP checkwarranty website.
But how it is generated? I have found that HP website uses another request just for ssid.
##### GET generateSSID
URL : https://support.hp.com/hp-pps-services/ssid/generateSSID
Without any params.
Respons like:
```
"<input type="hidden" id="secureTokenField" value="[mwsid, ossid]" /><input type="hidden" id="secureFieldJson" value='{"mwsid":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","ossid":"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}' />"
```

I wanted to give it a try, so i have started writting a code.
But it was not working, i was wondering why. So only differences between those request and my program requests were cookies.
I have tought that, maybe sending those requests without cookies is my problem.

So i have managed to first generate proper cookies by another GET request.
##### GET cookies
URL : https://support.hp.com/us-en/checkwarranty/multipleproducts/
Without any params.

After i get response, i just save client CookieContainer to a variable. Then with new request I use same CookieContainer and it works. I cut mwsid value form generateSSID response which is my new ssid. Then send multiWarranty request with this ssid as param and my json file as body. 

This should help others to write it in any language they want. I will upload my version of code soon. ( C# )




