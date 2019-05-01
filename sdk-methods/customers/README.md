# Events

Track customer activity via events.

{% tabs %}
{% tab title="Objective-C" %}
```objectivec
// sending custom events with custom fields
ZaiusEvent* event = [ZaiusEvent eventWithEventType:@"map" action:@"explore"];
// page is a field available on any event, but primarily used for pageviews
event[@"page"] = @"map_explorer";
// where map_region is a custom field you added to events in the Zaius App
event[@"map_region"] = @"North America";
[Zaius event:event];
```
{% endtab %}

{% tab title="Swift" %}
```swift
// sending custom events with custom fields
let event = ZaiusEvent(eventType: "map", action: "explore")
// page is a field available on any event, buy primarily used for pageviews
event["page"] = "map_explorer"
// where map_region is a custom field you added to events in the Zaius App
event["map_region"] = "North America"
Zaius.event(event)
```
{% endtab %}
{% endtabs %}

