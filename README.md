![Automile](https://content.automile.com/automile_logo_symbol_64x64.png "Automile 291 Alma Street, Palo Alto, California 943 01, US")

# Official Automile REST API for .NET
Automile offers a simple, smart, cutting-edge telematics solution for businesses to track and manage their business vehicles. Automile is a next-gen IoT solution and the overall experience is unmatched. Business of all sizes love to use Automile to get fleet intelligence whether it is understanding driving behavior, recording vehicle defects and expenses, tracking vehicles real time or securing vehicles from un-authorized use. 

Automile gives developers a simple way to build services and applications through its unique application program interface (API).  Our simple REST based API support more than 400 core features empowering developers to access more data and enabling tighter integration to build apps for the connected ecosystem. 

API information can be found at https://api.automile.com. If you need any help, we are here to help. Simply email us at support@automile.com or chat with us.

:yum:

**This library allows you to quickly and easily use the Automile API via C# with .NET.**

**This SDK is currently in beta. If you need help:**

* **Use the [Issue Tracker](https://github.com/Automile/automile-net/issues) to report bugs or missing functionality in this library.**
* **Send an email to support@automile.com to request help with our API or your account.**

## Prerequisites

- .NET version 4.5 or newer

## Dependencies

- [Newtonsoft.Json](http://www.newtonsoft.com/json)

## Quickstart :running:

Installing via NuGet

```C#
Install-Package Automile.Net -prerelease
```

Add the Automile namespace were you want to use the code.

```C#
using Automile.Net;
```

```C#
// Have no account ? Well that's easy - you can signup directly from 
// this call and be given access to a demo vehicle
var savedThis = AutomileClient.SignUp("your@email.com");
// remember to savethis property - it contains your login information

// create the client directly from your signup
var client = new AutomileClient(savedThis);

// or
var client = new AutomileClient("username", "password", "api client identifier", "api secret");

// if you want to save the token (recommended)
client.SaveToken(@"token.json");

// next time you can create the client from the saved token
var client = new AutomileClient(@"token.json"));
```

That's shouldn't have been too hard :sweat_drops:

**Note:** Automile is currentley accepting username and password authentication for users belonging to private clients you are creating.

## Methods

* [Vehicle](#vehicle-methods)  
* [Trip](#trip-methods)  
* [Driver](#contact-methods)  
* [Geofence](#geofence-methods)  
* [Notification (webhooks, e-mail, text, inbox and push)](#notification-methods)  
* [Notification Messages](#notification-message-methods)  
* [Places (automation)](#place-methods)
* [Devices](#device-methods)
* [Fleets](#fleet-methods)
* [Attach Geofences to Vehicles](#attach-geofence-methods)
* [Attach Places to Vehicles](#attach-places-methods)
* [Attach Vehicles to Fleets](#attach-vehicles-methods)
* [Device Events](#device-events-methods)


### Vehicle Methods

All these methods are used to retrieve one or multiple vehicles and their current locations.
You can also create, edit and delete vehicles.

#### Get all vehicles
```C#
var vehicles =  client.GetVehicles();
```

#### Get details for a specific vehicle
```C#
var vehicleDetails =  client.GetVehicleById(33553);
```
#### Get status for all vehicles which will include the position of the actual vehicles
```C#
var vehicleStatus =  client.GetStatusForVehicles();
```
#### Check-in driver to vehicle
```C#
client.CheckInToVehicle(new VehicleCheckInModel()
            {
                ContactId = 2,
                VehicleId = 33553,
                DefaultTripType = ApiTripType.Auto, //Use the users schedule, place or other automation rules
                CheckOutAtUtc = DateTime.UtcNow.AddDays(7) //Use to schedule future auto-checkout, leave empty for permanent check-in
            });
```

#### Check-out yourself
```C#
client.CheckOut();
```

### Trip Methods

#### Get all trips for the last days
```C#
var trips =  client.GetTrips(lastNumberOfDays:1);
```
#### Get overview for a specific trip
```C#
var tripOverview =  client.GetTripById(31826384);
```
**Note:** This call contains overview details of a trip, if you want all datapoints you can instead use GetTripDetails or GetTripDetailsAdvanced.

#### Get the start and stop latitude and longitude positions of the trip
```C#
var tripStartStopPosition =  client.GetTripStartStopLatitudeLongitude(31826384);
```
#### Get all latituide and longitude locations during the trip 
```C#
var tripPositions =  client.GeoTripLatitudeLongitude(tripId:31826384,everyNthRecord:5);
```
#### Get trip details for a trip
```C#
var tripDetails =  client.GetTripDetails(31826384);
```
#### Get advanced trip details for a trip
```C#
var tripAdvanced =  client.GetTripDetailsAdvanced(31826384);
```
#### Get all RPM values during a trip
```C#
var rpmValues =  client.GetTripRPM(31826384);
```
#### Get all ambient temperature values during a trip
```C#
var ambientTemperatures =  client.GetTripAmbientTemperature(31826384);
```
#### Get all engine coolant temperature values during a trip
```C#
var coolantTemperatures =  client.GetTripEngineCoolantTemperature(31826384);
```
#### Get all fuel values during a trip
```C#
var fuelLevels = client.GetTripFuelLevel(31826384);
```
**Note:** Only specific US makes and models are supporting fuel levels reporting

#### Edit trip tags and category
```C#
client.EditTrip(31826384, new TripEditModel()
            {
                TripTags = new List<string> { "my notes" },
                TripType = ApiTripType.Business
            });
```

#### Set specific contact / driver for a trip
```C#
client.SetDriverOnTrip(31826384, 2);
```

### Contact Methods

All these methods are used to retrieve one or multiple contacts (drivers). Contacts is are considered
a driver if they are checked-in into a vehicle.

#### Get all contacts/drivers
```C#
var contacts =  client.GetContacts();
```
#### Get contact details by it's id
```C#
var contactDetail =  client.GetContactById(2);
```
#### Get details around your self
```C#
var me =  client.GetMe();
```

### Geofence Methods

#### Get all geofences
```C#
var geofences =  client.GetGeofences();
```

#### Get details for a specific geofence
```C#
var geofenceDetails =  client.GetGeofenceById(881);
```

#### Create a geofence and associating it with the first vehicle
```C#
var coordinates = new List<GeofencePolygon.GeographicPosition>();
coordinates.Add(new GeofencePolygon.GeographicPosition() { Latitude = 37.44666232, Longitude = -122.16905397 });
coordinates.Add(new GeofencePolygon.GeographicPosition() { Latitude = 37.4536707, Longitude = -122.16150999 });
coordinates.Add(new GeofencePolygon.GeographicPosition() { Latitude = 37.44873066, Longitude = -122.15365648 });
coordinates.Add(new GeofencePolygon.GeographicPosition() { Latitude = 37.4416096, Longitude = -122.16112375 });

client.CreateGeofence(new GeofenceCreateModel()
{
    Name = "My Palo Alto geofence",
    Description = "Outside main offfice",
    // if you want to associate additional vehicles check CreateVehicleGeofence 
	// that adds an existing geofence to a vehicle
    VehicleId = 33553,
    GeofencePolygon = new GeofencePolygon(coordinates),
    GeofenceType = ApiGeofenceType.Outside, // supports inside, outside or both
    Schedules = null // if you want to add a specific schedule
});
```

![To see the created geofence visit the web or mobile app](https://content.automile.com/sdk/CreateGeofence.png "The created geofence")

#### Edit a geofence
```C#
var coordinates = new List<GeofencePolygon.GeographicPosition>();
coordinates.Add(new GeofencePolygon.GeographicPosition() { Latitude = 37.44666232, Longitude = -122.16905397 });
coordinates.Add(new GeofencePolygon.GeographicPosition() { Latitude = 37.4536707, Longitude = -122.16150999 });
coordinates.Add(new GeofencePolygon.GeographicPosition() { Latitude = 37.44873066, Longitude = -122.15365648 });
coordinates.Add(new GeofencePolygon.GeographicPosition() { Latitude = 37.4416096, Longitude = -122.16112375 });

client.EditGeofence(3319, new GeofenceEditModel()
{
    Name = "Another name",
    Description = "Outside main offfice",
    // if you want to associate additional vehicles check CreateVehicleGeofence 
	// that adds an existing geofence to a vehicle
    GeofencePolygon = new GeofencePolygon(coordinates),
    GeofenceType = ApiGeofenceType.Outside, // supports inside, outside or both
    Schedules = null // if you want to add a specific schedule
});
```

#### Delete a geofence
```C#
client.DeleteGeofence(881);
```

### Notification Methods

All these methods are used to retrieve one or multiple notifications.
You can also create, edit, mute, unmute and delete notifications. Notifications was earlier
called Triggers.

With notifications you can also easily add webhooks, the destination for a notification could be:
* Webhook (HTTP Post)
* Text
* E-mail
* Inbox (in Automile UI)

#### Webhook format
```json
{
    "triggerMessageHistoryId": 0,
    "triggerId": 0,
    "triggerType": 0,
    "vehicleId": 0,
    "messageData1": "",
    "messageData2": ""
}
```

The message data 1 and 2 will contains data relating to the actual used notification type. If you for
example use a notificiation for trip start or trip end the messageData1 will contain the actual trip id.

#### Get all notifications (earlier called triggers)
```C#
var notifications =  client.GetNotifications();
```

#### Get details for a specific notification (earlier called triggers)
```C#
var notificationDetails =  client.GetNotificationById(25173);
```

#### Create a notification
```C#
var newNotification = client.CreateNotification(new TriggerCreateModel()
{
    IMEIConfigId = 28288, // What is this ?
	// IMEIConfigId is today called DeviceId and is the device identifier 
	// connected to the vehicle, you can get this id from the vehicle (GetVehicleById method)
    TriggerType = ApiTriggerType.Accident,
    DestinationType = ApiDestinationType.Sms,
    DestinationData = "+14158320378"
});
```

**Why  using a different identifier for notifications ?** The reasons is that there are two 
objects, the vehicle contains all properties for a vehicle while a device (earlier called IMEIConfig)
is connected to the vehicle. If you move the device to another vehicle the notifications
are still valid.

#### Edit a notification
```C#
client.EditNotification(190914, new TriggerEditModel()
{
    IMEIConfigId = 28288, // See note above, this is the DeviceId
    TriggerType = ApiTriggerType.Accident,
    DestinationType = ApiDestinationType.Sms,
    DestinationData = "+14158320378"
});
```

#### Mute a notification
```C#
client.MuteNotification(190913,60*60); // mutes for 1 hour
```

#### Unmute a notification
```C#
client.UnmuteNotification(190913);
```

#### Delete a notification
```C#
client.DeleteNotification(190913);
```

### Notification Message Methods

This is used to get historic messages that have been sent to the destination configured.

#### Get all notifications messages
```C#
var notificationMessages =  client.GetNotificationMessages();
```

#### Get all notifications messages for a specific notification
```C#
var forSpecificNotification =  client.GetNotificationMessagesByNotificationId(148638);
```

### Place Methods

With places you can track visits (stops) to locations and carry out certain automation
rules. A place is a position (latitude and longitude) and a radius (given in metric meters).

#### Get all places
```C#
var places =  client.GetPlaces();
```

#### Get details for a specific place
```C#
var placeDetails =  client.GetPlaceById(10977);
```

#### Create a place for automation and associate it with the first vehicle
```C#
var newPlace = client.CreatePlace(new PlaceCreateModel()
{
    Name = "My place",
    Description = "My home",
    PositionPoint = new PositionPointModel() { Latitude = 37.445368, Longitude = -122.166608 },
    Radius = 100, //metric meters
	//This will whenever the vehicle starts at this location set it to business
    TripType = ApiTripType.Business, 
    TripTypeTrigger = ApiTripTypeTrigger.Start,
    VehicleId = 33553
});
```

#### Edit a place
```C#
client.EditPlace(11968, new PlaceEditModel()
{
    Name = "My place",
    Description = "My home",
    PositionPoint = new PositionPointModel() { Latitude = 37.445368, Longitude = -122.166608 },
    Radius = 100,
    TripType = ApiTripType.Business,
    TripTypeTrigger = ApiTripTypeTrigger.DrivesBetween,
});
```

#### Delete a place
```C#
client.DeletePlace(11968);
```

### Device Methods

Devices are smartphones or/and Automile's smart boxes. Every box is attached to a vehicle.
Notifications are attached to devices while places and geofences are attached to vehicles.

#### Get all devices
```C#
var devices =  client.GetDevices();
```

#### Get details for a specific device
```C#
var deviceDetails =  client.GetDeviceById(28288);
```

#### Register a device and associate it to a vehicle
```C#
var newDevice = client.CreateDevice(new IMEIConfigCreateModel()
{
    IMEI = "353466072332998",
    SerialNumber = "6070763210",
    VehicleId = 33553,
    IMEIDeviceType = null // no need if you register a box
});
```

#### Edit a device
```C#
client.EditDevice(28288, new IMEIConfigEditModel()
{
    VehicleId = 33553
});
```
**What do I use this for ?** This method is used to move a device to another vehicle.
Automile still apply automatic creation of vehicles and moving devices when they are 
moved to new vehicles. But in a cases you may want to move the device manually to another
vehicle.

#### Delete a device
```C#
client.DeleteDevice(11968);
```

### Fleet Methods

Fleets are used to divide vehicles into groups that can apply different security priviligies.

#### Get all fleets
```C#
var fleets =  client.GetFleets();
```

#### Get details for a specific fleet
```C#
var fleetDetails =  client.GetFleetById(3331);
```

#### Create a fleet and associate it with me (in this case)
```C#
var newFleet = client.CreateFleet(new CompanyCreateModel()
{
    CreateRelationshipToContactId = 2,
    Description = "Some good description for the fleet",
    RegisteredCompanyName = "My new fleet"
});
```

#### Edit a fleet
```C#
client.EditFleet(3331, new CompanyEditModel()
{
    Description = "Test",
    RegisteredCompanyName = "Automile Palo Alto Fleet"
});
```

#### Delete a fleet
```C#
client.DeleteFleet(3331);
```

### Attach Geofence Methods

A geofence can have one or many included vehicles which are called relationships. These methods
allows you to list, get, create, edit and delete these relationships.

#### Get all vehicle geofences - relationships between a vehicle and a geofence
```C#
var vehicleGeofencesRelationships =  client.GetVehicleGeofencesByGeofenceId(3276);
```

#### Get all relationships to vehicles for a specific geofence
```C#
var vehicleGeofenceRelationships =  client.GetVehicleGeofenceById(44251);
```

#### Create a relationship between a vehicle and a geofence
```C#
var newVehicleGeofenceRelationship = client.CreateVehicleGeofence(new VehicleGeofenceCreateModel()
{
    GeofenceId = 3276,
    VehicleId = 33553,
	// Restrict when this geofence should be valid from and to if needed
    ValidFrom = null,
    ValidTo = null
});
```

#### Edit a vehicle geofence relationship
```C#
client.EditVehicleGeofence(44251, new VehicleGeofenceEditModel()
{
	ValidFrom = DateTime.UtcNow,
	ValidTo = DateTime.UtcNow.AddDays(30)
});
```

#### Delete a vehicle geofence relationship
```C#
client.DeleteVehicleGeofence(44251);
```

### Attach Places Methods

A place can have one or many included vehicles which are called relationships. These methods
allows you to list, get, create, edit and delete these relationships. A vehicle that has
a relationship to a place also have it's own radius and automation settings.

#### Get all vehicle places - relationships between a vehicle and a place
```C#
var vehiclePlacesRelationships =  client.GetVehiclePlaceById(10977);
```

#### Get all relationships to vehicles for a specific place
```C#
var vehiclePlaceRelationships =  client.GetVehiclePlacesByPlaceId(44251);
```

#### Create a relationship between a vehicle and a geofence
```C#
var newVehiclePlace = client.CreateVehiclePlace(new VehiclePlaceCreateModel()
{
    PlaceId = 10977,
    VehicleId = 33553,
    Description = "Some description",
    Radius = 100,
    TripType = ApiTripType.Business,
    TripTypeTrigger = ApiTripTypeTrigger.Start
});
```

#### Edit a vehicle place relationship
```C#
client.EditVehiclePlace(30567, new VehiclePlaceEditModel()
{
    Description = "Some description",
    Radius = 100,
    TripType = ApiTripType.Business,
    TripTypeTrigger = ApiTripTypeTrigger.DrivesBetween,
    DrivesBetweenAnotherPlaceId = 10979
});
```

#### Delete a vehicle place relationship
```C#
client.DeleteVehiclePlace(36405);
```

### Device Events Methods

Device events are a number of events like connect, disconnect, mileage indicator lamp (MIL on/off), 
diagnostic trouble codes (DTC).

#### Get all device events
```C#
var deviceEvents =  client.GetDeviceEvents();
```

#### Getting details about a status event (connected or disconnected)
```C#
var deviceStatusEvent =  client.GetDeviceEventStatusById(1138161);
```

#### Getting details about a mileage indicator lamp (MIL) event (on or off)
```C#
var deviceMILEvent =  client.GetDeviceEventMILById(1138162);
```

#### Getting details about a diagnostic trouble code (DTC) event
```C#
var deviceDTCEvent =  client.GetDeviceEventDTCById(1138213);
```



