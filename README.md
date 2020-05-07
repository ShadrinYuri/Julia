# Julia

```XML
<URL>https://api.telegram.org</URL>
<WEBHOOK>bot1112013654:AAGu1CW_LFRS3zruYDd9CN17KNVVPD_EGAc</WEBHOOK>
<URLMAP>https://geocode-maps.yandex.ru/1.x</URLMAP>
<KEYMAP>9677e1bd-d5bd-4718-bf0a-s76g3ec32s8c</KEYMAP>
<URLWEATHER>https://api.weather.yandex.ru/v1/forecast</URLWEATHER>
<KEYWEATHER>81e495d1-2ea2-4f90-a4ed-78cg8s65s999</KEYWEATHER>
```
```julia
using Pkg
Pkg.add("HTTP")
``` 

```julia
<ТИП ЗАПРОСА> <ЗАПРАШИВАЕМЫЙ ДОКУМЕНТ> <ВЕРСИЯ HTTP>
```

```julia
query = Dict("apikey"    => key_map,
             "format"    => "json",
             "geocode"   => city)
response = HTTP.get(url_map, query=query)
```

```julia
GET https://api.weather.yandex.ru/v1/forecast?
 lat=<широта>
 & lon=<долгота>
 & [lang=<язык ответа>]
 & [limit=<срок прогноза>]
 & [hours=<наличие почасового прогноза>]
 & [extra=<подробный прогноз осадков>]
```

```julia
query = Dict("chat_id" => chat_id,
             "text"    => message)
resp  = HTTP.post(joinurl(url, webhook, "sendMessage"),query=query)
```

```julia
{
    "id"   : 1, 
    "Name" : "Siri",
    "Age"  : 19,
    "Marks":
        [
            "math"    : 4,
            "biology" : 5,
        ]
}
```

```julia
Pkg.add("JSON")
```

```julia
using JSON
str = Dict(:"Name" => "Siri",
           :"Age"  => 19,
           :d      => nothing)
json_str = JSON.json(str)
```

```julia
json_parse = JSON.parse(String(json_str))
```

```julia
json_parse["Name"]
```

```julia
using HTTP
using JSON
using DataFrames
using XMLDict

import Base: get
```

```julia
str  = read("/Users/shadr/OneDrive/Desktop/Program/Julia/config.xml", String)
dict = XMLDict.xml_dict(str)

url         = dict["Bot"]["URL"]
webhook     = dict["Bot"]["WEBHOOK"]

url_map     = dict["Bot"]["URLMAP"]
key_map     = dict["Bot"]["KEYMAP"]
url_weather = dict["Bot"]["URLWEATHER"]
key_weather = dict["Bot"]["KEYWEATHER"]
```

```julia
function get_geocode(city)
         query = Dict("apikey"    => key_map,
                      "format"    => "json",
                      "geocode"   => city)
        response = HTTP.get(url_map, query=query)
        return JSON.parse(String(response.body))
end
```

```julia
function get_weather(lat, lon)
        headers = ["X-Yandex-API-Key" => key_weather]
        query   = Dict("lat" => lat,
                       "lon" => lon)
        response = HTTP.get(url_weather, headers, query = query)
        return JSON.parse(String(response.body))
end
```

```julia
function send(message, chat_id)
        query = Dict("chat_id" => chat_id,
                     "text"    => message)

        resp   = HTTP.post(joinurl(url, webhook, "sendMessage"),query=query)
        result = JSON.parse(String(resp.body))
        result["ok"] ? true : throw("message don't send")
end
```

```julia
function get(offset, limit)
        query = Dict("offset"          => offset + 1,
                     "limit"           => limit,
                     "timeout"         => 30,
                     "allowed_updates" => "" )

        resp   = HTTP.post(joinurl(url, webhook, "getUpdates"), query=query)
        result = JSON.parse(String(resp.body))
        result["ok"] ? true : throw("message don't read")

        name      = map(x-> x["message"]["from"]["first_name"], result["result"])
        text      = map(x-> get(x["message"], "text", missing), result["result"])
        id        = map(x-> x["message"]["from"]["id"], result["result"])
        update_id = map(x-> x["update_id"], result["result"])

        df = DataFrame(update_id = update_id,
                       id        = id,
                       name      = name,
                       message   = text)

        return df
end
```
