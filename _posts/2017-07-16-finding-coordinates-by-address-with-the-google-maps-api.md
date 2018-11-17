---
title: Finding coordinates by address with the Google Maps API
date: 2017-07-16
layout: post
comments: true
published: true
description: 
keywords: 
---

```php
<?php

// Google maps - Geocoding
function google_maps_search($address, $key = '')
{
    $url = sprintf('https://maps.googleapis.com/maps/api/geocode/json?address=%s&key=%s', urlencode($address), urlencode($key));
    $response = file_get_contents($url);
    $data = json_decode($response, 'true');
    return $data;
}

function map_google_search_result($geo)
{
    if (empty($geo['status']) || $geo['status'] != 'OK' || empty($geo['results'][0])) {
        return null;
    }
    $data = $geo['results'][0];
    $postalcode = '';
    foreach ($data['address_components'] as $comp) {
        if (!empty($comp['types'][0]) && ($comp['types'][0] == 'postal_code')) {
            $postalcode = $comp['long_name'];
            break;
        }
    }
    $location = $data['geometry']['location'];
    $formatAddress = !empty($data['formated_address']) ? $data['formated_address'] : null;
    $placeId = !empty($data['place_id']) ? $data['place_id'] : null;

    $result = [
        'lat' => $location['lat'],
        'lng' => $location['lng'],
        'postal_code' => $postalcode,
        'formated_address' => $formatAddress,
        'place_id' => $placeId,
    ];
    return $result;
}

//
// Usage
//

// Your google API key
// https://developers.google.com/maps/documentation/geocoding/usage-limits?hl=de
// 2,500 free requests per day, calculated as the sum of client-side and server-side queries.
// 50 requests per second, calculated as the sum of client-side and server-side queries.
$googleKey = '';

$zip = '10117';
$street = 'Friedrichstrasse 106';
$city = 'Berlin';
$country = 'DE';
$search = implode(', ', [$street, $zip, $city, $country]);

$geoData = google_maps_search($search, $googleKey);
if (!$geoData) {
    echo "Error: " . $id . "\n";
    exit;
}

$mapData = map_google_search_result($geoData);

echo $mapData['lat']; // 52.5227797
echo "\n";
echo $mapData['lng']; // 13.3880986
```