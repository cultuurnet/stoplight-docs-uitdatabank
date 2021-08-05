# Errors

This page contains an overview of all possible error types inside the `https://api.publiq.be/probs/uitdatabank/` namespace that can be returned by UiTdatabank APIs.

> For more info about error handling on our APIs in general, see the dedicated [Errors](https://publiq.stoplight.io/docs/errors) space.

## calendar-type-not-supported

*   **Complete type:** `https://api.publiq.be/probs/uitdatabank/calendar-type-not-supported`
*   **Title**: `Calendar type not supported`
*   **Status**: `400`

The request you are trying to perform is not supported on the calendar type of the given [event](/models/event-calendarType.json) or [place](/models/place-calendarType.json). For example, you cannot update the bookingAvailability of events or places with calendar types `periodic` and `permanent`.

Usually the `detail` of the error will contain more information.

Try using an event or place id with a calendar type that is supported by the operation that you are trying to perform.
