silverstripe-googlemapfield
==============

[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/BetterBrief/silverstripe-googlemapfield/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/BetterBrief/silverstripe-googlemapfield/?branch=master)

Lets you record a precise location using latitude/longitude/zoom fields to a DataObject.

Displays a map using the Google Maps API. The user may then choose where to place the marker; the landing coordinates are then saved.

You can also search for locations using the search box, which uses the Google Maps Geocoding API.

Supports Silverstripe 4 and 5

## Usage

### Minimal configuration

Given your DataObject uses the field names `Latitude` and `Longitude` for storing the latitude and longitude
respectively then the following is a minimal setup to have the map show in the CMS:

```php
use SilverStripe\ORM\DataObject;
use BetterBrief\GoogleMapField;

class Store extends DataObject
{
    private static $db = [
        'Title' => 'Varchar(255)',
        'Latitude' => 'Varchar',
        'Longitude' => 'Varchar',
    ];
    
    public function getCMSFields() {
        $fields = parent::getCMSFiels();
        
        // add the map field
        $fields->addFieldToTab('Root.Main', new GoogleMapField(
            $this,
            'Location'
        ));
        
        // remove the lat / lng fields from the CMS
        $fields->removeFieldsFromTab('Root.Main', ['Latitude', 'Longitude']);
        
        return $fields;
    }
}
```

Remember to set your API key in your site's `config.yml`

```yml
BetterBrief\GoogleMapField:
  default_options:
    api_key: '[google-api-key]'
```

## Optional configuration

### Configuration options

You can either set the default options in your yaml file (see [_config/googlemapfield.yml](_config/googlemapfield.yml)
for a complete list) or at run time on each instance of the `GoogleMapField` object.

#### Setting at run time

To set options at run time pass through an array of options (3rd construct parameter):

```php
use BetterBrief\GoogleMapField;

$field = new GoogleMapField(
    $dataObject,
    'FieldName',
    [
        'api_key' => 'my-api-key',
        'show_search_box' => false,
        'map' => [
            'zoom' => 10,
        ],
        ...
    ]
);
```

#### Customising the map appearance

You can customise the map's appearance by passing through settings into the `map` key of the `$options` (shown above).
The `map` settings take a literal representation of the [google.maps.MapOptions](https://developers.google.com/maps/documentation/javascript/reference?csw=1#MapOptions)

For example if we wanted to change the map type from a road map to satellite imagery we could do the following:

```php
use BetterBrief\GoogleMapField;

$field = new GoogleMapField(
    $object,
    'Location',
    [
        'map' => [
            'mapTypeId' => 'SATELLITE',
        ],
    ]
);
```

# Getting an API key

## Google Maps API key

To get a Google Maps JS API key please see [the official docs](https://developers.google.com/maps/documentation/javascript/get-api-key)

## Geocoding access - enabling the search box

To use the search box to find locations on the map, you'll need to have enabled the Geocoding API as well. Please see
[the official docs](https://developers.google.com/maps/documentation/javascript/geocoding#GetStarted)

## Notes for Silverstripe 5

- Requires PHP 8.1+ (already enforced in `composer.json`).
- If your project enables a Content Security Policy (CSP), ensure `https://maps.googleapis.com` and `https://*.gstatic.com` are allowed for `script-src` and `img-src` so Google Maps loads in the CMS.
- Asset paths use the Silverstripe module resource notation (e.g. `betterbrief/silverstripe-googlemapfield:client/...`). Ensure you’re on a recent `silverstripe/vendor-plugin` compatible with SS5.
