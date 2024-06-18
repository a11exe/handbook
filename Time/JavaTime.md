# Java time

## Epoch

An epoch is a an absolute time reference. Most programming languages (e.g Java, JavaScript, Python)
use the Unix epoch (Midnight 1 January 1970) when expressing a given timestamp as the number of milliseconds
elapsed since a fixed point-in-time reference.

## Time zone

The coordinated universal time (UTC) is the most common time standard.
The UTC time zone (equivalent to GMT) represents the time reference all other time zones relate to (through a
positive/negative offset).

UTC time zone is commonly referred to as Zulu time (Z) or UTC+0. Japan’s time zone is UTC+9 and the Honolulu time zone
is UTC-10.

## ISO 8601

ISO 8601 is the most widespread date/time representation standard and it uses the following date/time formats:

| TIME ZONE     | NOTATION                      | 
|---------------|-------------------------------|
| UTC           | 1970-01-01T00:00:00.000+00:00 |
| UTC Zulu time | 1970-01-01T00:00:00.000Z      |
| Tokio         | 1970-01-01T09:00:00.000+09:00 |
| Honolulu      | 1969-12-31T14:00:00.000-10:00 |

## Relative vs Absolute time instances

### Relative timestamp
The numeric timestamp representation (the numbers of milliseconds since epoch) is relative info. 
This value is given against the UTC epoch but you still need a time zone to properly represent the actual time on a particular region.

If you don’t know the original event time zone, you risk displaying a timestamp against the current local time zone and this is not always desirable.

### Absolute timestamp
The absolute timestamp contains both the relative time as well as the time zone info. 
It’s quite common to express timestamps in their ISO 8601 string representation.

Absolute time representation is much more convenient when we want to reconstruct the time instance against the original time zone. 
An e-mail client might want to display the email creation date using the sender’s time zone, and this can only be achieved using absolute timestamps.

