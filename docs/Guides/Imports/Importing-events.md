# Importing events

You can either import new events into UiTDatabank or overwrite existing events through imports.

## Endpoints

To import a new event, you will need to perform a [POST request documented here](https://publiq.stoplight.io/docs/uitdatabank/reference/Import-API.v3.json/paths/~1events~1/post).
To update an existing event through an import, you will need to perform a [PUT request documented here](https://publiq.stoplight.io/docs/uitdatabank/reference/Import-API.v3.json/paths/~1events~1%7BeventId%7D/put).

Both of these requests require a JSON body that represents a complete event. This guide will go into more detail how the JSON of the event should be structured.

<!-- theme: info -->

> ### Authentication
>
> When importing new or existing events into UiTDatabank you will need to authenticate. To learn how to authenticate your requests, read [Authentication](../Authentication.md)

## Required properties

### mainLanguage

When importing a **new** event, you will need to define the main language that its name, description, etc will be written in.

Languages can always be one of `nl`, `fr`, `de` or `en`. The most common use case will usually be `nl`.

For example:

```json
{
  "mainLanguage": "nl"
}
```

<!-- theme: info -->

> Every multilingual property that you will define later on will need to have a value specified for this language code at the very least.

<!-- theme: warning -->

> When **updating** an existing event through an import, changes to the `mainLanguage` property will be ignored since it cannot be changed!

### name

Each event needs to have a name that describes in a couple of words what the event is about.

The `name` property is a multilingual property that requires at least one language code, specifically the one set as `mainLanguage`.

For example:

```json
{
  "mainLanguage": "nl",
  "name": {
    "nl": "Mijn fantastische activiteit!"
  }
}
```

Optionally you can provide translations to other languages:

```json
{
  "mainLanguage": "nl",
  "name": {
    "nl": "Mijn fantastische activiteit!",
    "en": "My amazing activity!"
  }
}
```

<!-- theme: info -->

> When **updating** an existing event, you can:
> - Update the name in the main language (but not remove it)
> - Add more translations to the name
> - Update translations of the name
> - Remove translations from the name

### calendar

Depending on when and how often your event takes place, it can have very different calendar info. To provide calendar info, you need to start by setting the right `calendarType` property.

This property can either be `single` or `multiple` for events happening once or multiple times respectively.

Aditionally it can be `periodic` for events that span a longer period of time and have recurring weekly hours that they take place, or `permanent` for events that have no specific start or end date and have recurring weekly hours that they take place.

 <!-- theme: info -->

> When **updating** an existing event, you **can** change the calendar type and other calendar information.

Guidelines for using the correct calendarType:

calendarType | when to use | allowed on events | allowed on places
---------|----------|---------|---------
 single | for events that take place at one single day (f.e. a concert of Radiohead) and events with only one start and enddate (f.e. camping from monday until sunday) | Yes | No
 multiple | for events that take place on several occasions, whether or not contiguous (f.e. a festival) | Yes | No
 periodic | for events or places that take places on recurring moments (f.e. a course watercolor painting, each wednesday evening | Yes | Yes
 permanent | for places you can visit permanently (f.e. de Grote Markt in Brussels) | Sometimes | Yes

#### single

Events that take place once need a `startDate` and an `endDate`.

For example an event taking place on January 10th 2021, from 16:00 until 20:00 in UTC+1:

```json
{
  "mainLanguage": "nl",
  ...
  "calendarType": "single",
  "startDate": "2021-01-10T16:00:00+01:00",
  "endDate": "2021-01-10T20:00:00+01:00"
}
```

#### multiple

Events that take place on several occasions need the same `startDate` and `endDate` properties as `single`, but this time those dates should be the **earliest start date** of the event and the **latest end date** of the event.

Additionally, they need a `subEvent` array that contains objects with (again) `startDate` and `endDate`, this time for every date/time that the event takes place.

For example an event taking place at three different moments:

```json
{
  "mainLanguage": "nl",
  ...
  "calendarType": "multiple",
  "startDate": "2021-01-10T16:00:00+01:00",
  "endDate": "2021-02-18T20:00:00+01:00"
  "subEvent": [
    {
      "startDate": "2021-01-10T16:00:00+01:00",
      "endDate": "2021-01-10T20:00:00+01:00"
    },
    {
      "startDate": "2021-01-28T16:00:00+01:00",
      "endDate": "2021-01-28T20:00:00+01:00"
    },
    {
      "startDate": "2021-02-18T16:00:00+01:00",
      "endDate": "2021-02-18T20:00:00+01:00"
    }
  ]
}
```

#### periodic

To be documented.

#### permanent

To be documented.

<!-- theme: warning -->

> ##### Important
> While events _can_ have a `permanent` `calendarType`, it is generally not recommended except for some very specific use cases. Consider [creating a place](Importing-places.md) instead.

### terms

UiTDatabank enables the use of taxonomy terms to classify events in categories. Every event is always required to have at least one taxonomy term specified in its `terms` property, specifically one from the `eventtype` domain.

You can specify which term(s) to use by providing their id. For example to add the `eventtype` for concerts:

```json
{
  "mainLanguage": "nl",
  ...
  "terms": [
    {
      "id": "0.50.4.0.0"
    }
  ]
}
```

While terms can contain additional properties like `label` and `domain` when UiTDatabank returns an event in a response, you do **not** need to specify those when importing. They will get added automatically when you import a new or existing event and specify the terms that you want the event to have by their ids.

<!-- theme: info -->

> See [Taxonomy](../Taxonomy.md) for more info on the possible taxonomy terms and how to make sure you are using up-to-date term ids.

### location

Every event in UiTDatabank requires a location, which is a separate entity known as a "place" that gets linked to the event.

To specify the place your event is taking place at, include a `location` property with an `@id` property that references the URL of the place in UiTDatabank.

For example:

```json
{
  "mainLanguage": "nl",
  ...
  "location": {
    "@id": "https://io-test.uitdatabank.be/place/efaa2c11-bd42-4828-8579-4ee50e811f01"
  }
}
```

<!-- theme: warning -->

> The API  expects the full URL of the location for legacy and backward compatibility reasons, **but it does not actually enable linking of events and locations across different environments like production and test.**
> While you can provide a URL from a different environment than the one you are importing to (testing vs production), **it is the uuid at the end of the URL that ultimately determines what place gets linked**. So if the given uuid does not exist on the environment you are importing your event to, or points to a different place on that environment, this can give unexpected results.


<!-- theme: info -->

> If your event's location does not exist yet in UiTDatabank, you can [import it as a place](Importing-places.md). **However make sure you do not the same place multiple times** to prevent unnecessary duplicates. This helps visitors find other events happening at the same location when they are all linked to the same place instead of duplicate places.

## Optional properties

Aside from the required properties to import a new event, you can specify additional optional properties to enrich it with more info. The more details you provide, the more attractive it will be for potential attendees.

### audience

To be documented.

### availableFrom

To be documented.

### bookingInfo

To be documented.

### contactPoint

To be documented.

### description

To be documented.

### labels

To be documented.

### mediaObject

To be documented.

### organizer

Similar to `location`, events in UiTDatabank can be linked to an `organizer` entity. This allows people to find other events from the same organizer.

To specify the organizer of your event, include an `organizer` property with an `@id` property that references the URL of the organizer in UiTDatabank.

For example:

```json
{
  "mainLanguage": "nl",
  ...
  "organizer": {
    "@id": "https://io-test.uitdatabank.be/organizers/02bdbd1b-672e-461e-85b2-9ce3746fb1a6"
  }
}
```

<!-- theme: warning -->

> The API  expects the full URL of the organizer for legacy and backward compatibility reasons, **but it does not actually enable linking of events and organizers across different environments like production and test.**
> While you can provide a URL from a different environment than the one you are importing to (testing vs production), **it is the uuid at the end of the URL that ultimately determines what organizer gets linked**. So if the given uuid does not exist on the environment you are importing your event to, or points to a different organizer on that environment, this can give unexpected results.

<!-- theme: info -->

> If your event's organizer does not exist yet in UiTDatabank, you can [import it](Importing-organizers.md).

### priceInfo

To be documented.

### status

To be documented.

### typicalAgeRange

To be documented.