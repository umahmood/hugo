+++
title = "Styling Markers on Mapbox Static Maps"
date = 2019-10-05T16:12:46+01:00
draft = false
hideToc = true
+++

Recently I was using the python map-box api to generate static maps. I needed to
style the markers which pin pointed locations on the map. I found it difficult to
find the necessary documentation on how to do this. In the end it simply involved
adding the following fields:

- maker-colour (hex value).
- marker-symbol ([Maki icon name](https://labs.mapbox.com/maki-icons/). an integer, or a lowercase letter).
- marker-size (small, large).

To the JSON like so:

```python
origin = {
    'type': 'Feature',
    'properties': {
        'name': 'Cambridge',
        'marker-color': '#f600f6',
        'marker-symbol': 'c',
        'marker-size': 'large',
        },
    'geometry': {
        'type': 'Point',
        'coordinates': [0.1218,52.2053]
        },
    }
```

Here is the output:

![](https://i.imgur.com/S7y4KOX.png)

Here is the full code sample:

```python
from mapbox import StaticStyle

location1 = {
    'type': 'Feature',
    'properties': {
        'name': 'Cambridge',
        'marker-color': '#f600f6',
        'marker-symbol': 'c',
        'marker-size': 'large',
        },
    'geometry': {
        'type': 'Point',
        'coordinates': [0.1218,52.2053]
        },
}

location2 = {
    'type': 'Feature',
    'properties': {
        'name': 'Kings Cross London',
        'marker-color': '#26fae4',
        'marker-symbol': 'k',
        'marker-size': 'large',
        },
    'geometry': {
        'type': 'Point',
        'coordinates': [-0.125250,51.544180]
        }
}

features = [location1, location2]
service  = StaticStyle()
response = service.image(username='mapbox',
                         style_id='streets-v9',
                         retina=True,
                         attribution=False,
                         logo=False,
                         features=features)

with open('map.png', 'wb') as img:
    img.write(response.content)
```

**Note:** Remember to the set the environment variable:

```
MAPBOX_ACCESS_TOKEN="MY_ACCESS_TOKEN"
```

Before you run your script, the environment variable is read by the mapbox library.

[Mapbox static maps documentation](https://docs.mapbox.com/api/maps/#static).

Fin.
