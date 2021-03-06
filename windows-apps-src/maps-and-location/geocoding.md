---
title: Perform geocoding and reverse geocoding
description: This guide shows you how to convert street addresses to geographic locations (geocoding) and convert geographic locations to street addresses (reverse geocoding) by calling the methods of the MapLocationFinder class in the Windows.Services.Maps namespace.
ms.assetid: B912BE80-3E1D-43BB-918F-7A43327597D2
ms.date: 07/02/2018
ms.topic: article
keywords: windows 10, uwp, geocoding, map, location
ms.localizationpriority: medium
---
# Perform geocoding and reverse geocoding

This guide shows you how to convert street addresses to geographic locations (geocoding) and convert geographic locations to street addresses (reverse geocoding) by calling the methods of the [**MapLocationFinder**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinder) class in the [**Windows.Services.Maps**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps) namespace.

> [!TIP]
> To learn more about using maps in your app, download the [MapControl](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MapControl) sample from the [Windows universal samples repo](hhttps://github.com/Microsoft/Windows-universal-samples) on GitHub.

The classes involved in geocoding and reverse geocoding are organized as follows.

-   The [**MapLocationFinder**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinder) class contains methods that handle geocoding ([**FindLocationsAsync**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsasync)) and reverse geocoding ([**FindLocationsAtAsync**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsatasync)).
-   These methods both return a [**MapLocationFinderResult**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinderResult) instance.
-   The [**Locations**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinderresult.locations) property of the [**MapLocationFinderResult**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinderResult) exposes a collection of [**MapLocation**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation) objects. 
-   [**MapLocation**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation) objects have both an [**Address**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocation.address) property, which exposes a [**MapAddress**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapAddress) object representing a street address, and a [**Point**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocation.point) property, which exposes a [**Geopoint**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geopoint) object representing a geographic location.

> [!IMPORTANT]
> You must specify a maps authentication key before you can use map services. For more info, see [Request a maps authentication key](authentication-key.md).

## Get a location (Geocode)

This section shows how to convert a street address or a place name to a geographic location (geocoding).

1.  Call one of the overloads of the [**FindLocationsAsync**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsasync) method of the [**MapLocationFinder**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinder) class with a place name or street address.
2.  The [**FindLocationsAsync**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsasync) method returns a [**MapLocationFinderResult**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinderResult) object.
3.  Use the [**Locations**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinderresult.locations) property of the [**MapLocationFinderResult**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinderResult) to expose a collection [**MapLocation**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation) objects. There may be multiple [**MapLocation**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation) objects because the system may find multiple locations that correspond to the given input.

```csharp
using Windows.Services.Maps;
using Windows.Devices.Geolocation;
...
private async void geocodeButton_Click(object sender, RoutedEventArgs e)
{
   // The address or business to geocode.
   string addressToGeocode = "Microsoft";

   // The nearby location to use as a query hint.
   BasicGeoposition queryHint = new BasicGeoposition();
   queryHint.Latitude = 47.643;
   queryHint.Longitude = -122.131;
   Geopoint hintPoint = new Geopoint(queryHint);

   // Geocode the specified address, using the specified reference point
   // as a query hint. Return no more than 3 results.
   MapLocationFinderResult result =
         await MapLocationFinder.FindLocationsAsync(
                           addressToGeocode,
                           hintPoint,
                           3);

   // If the query returns results, display the coordinates
   // of the first result.
   if (result.Status == MapLocationFinderStatus.Success)
   {
      tbOutputText.Text = "result = (" +
            result.Locations[0].Point.Position.Latitude.ToString() + "," +
            result.Locations[0].Point.Position.Longitude.ToString() + ")";
   }
}
```

This code displays the following results to the `tbOutputText` textbox.

``` syntax
result = (47.6406099647284,-122.129339994863)
```

## Get an address (reverse geocode)

This section shows how to convert a geographic location to an address (reverse geocoding).

1.  Call the [**FindLocationsAtAsync**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsatasync) method of the [**MapLocationFinder**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinder) class.
2.  The [**FindLocationsAtAsync**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsatasync) method returns a [**MapLocationFinderResult**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinderResult) object that contains a collection of matching [**MapLocation**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation) objects.
3.  Use the [**Locations**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinderresult.locations) property of the [**MapLocationFinderResult**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinderResult) to expose a collection [**MapLocation**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation) objects. There may be multiple [**MapLocation**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation) objects because the system may find multiple locations that correspond to the given input.
4.  Access [**MapAddress**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapAddress) objects through the [**Address**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocation.address) property of each [**MapLocation**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation).

```csharp
using Windows.Services.Maps;
using Windows.Devices.Geolocation;
...
private async void reverseGeocodeButton_Click(object sender, RoutedEventArgs e)
{
   // The location to reverse geocode.
   BasicGeoposition location = new BasicGeoposition();
   location.Latitude = 47.643;
   location.Longitude = -122.131;
   Geopoint pointToReverseGeocode = new Geopoint(location);

   // Reverse geocode the specified geographic location.
   MapLocationFinderResult result =
         await MapLocationFinder.FindLocationsAtAsync(pointToReverseGeocode);

   // If the query returns results, display the name of the town
   // contained in the address of the first result.
   if (result.Status == MapLocationFinderStatus.Success)
   {
      tbOutputText.Text = "town = " +
            result.Locations[0].Address.Town;
   }
}
```

This code displays the following results to the `tbOutputText` textbox.

``` syntax
town = Redmond
```

## Related topics

* [UWP map sample](https://go.microsoft.com/fwlink/p/?LinkId=619977)
* [UWP traffic app sample](https://go.microsoft.com/fwlink/p/?LinkId=619982)
* [Design guidelines for maps](https://docs.microsoft.com/windows/uwp/maps-and-location/controls-map)
* [Video: Leveraging Maps and Location Across Phone, Tablet, and PC in Your Windows Apps](https://channel9.msdn.com/Events/Build/2015/2-757)
* [Bing Maps Developer Center](https://www.bingmapsportal.com/)
* [**MapLocationFinder** class](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinder)
* [**FindLocationsAsync** method](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsasync)
* [**FindLocationsAtAsync** method](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsatasync)
