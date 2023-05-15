---
layout: default
title: DATE_TRUNC
description: Reference material for the DATE_TRUNC function
parent: SQL functions
---

# DATE_TRUNC

{: .warning}
  >You are looking at the documentation for Firebolt's redesigned date and timestamp types.
  >These types were introduced in DB version 3.19 under the names `PGDATE`, `TIMESTAMPNTZ` and `TIMESTAMPTZ`, and synonyms `DATE`, `TIMESTAMP` and `TIMESTAMPTZ` made available in DB version 3.22.
  >
  >If you worked with Firebolt before DB version 3.22, you might still be using the legacy date and timestamp types.
  >Determine which types you are using by executing the query `SELECT EXTRACT(CENTURY FROM DATE '2023-03-16');`.
  >If this query returns a result, you are using the redesigned date and timestamp types and can continue with this documentation.
  >If this query returns an error, you are using the legacy date and timestamp types and can find [legacy documentation here](../../general-reference/legacy-date-timestamp.md#legacy-date-and-timestamp-functions), or instructions to use the new types [here](../../release-notes/release-notes.md#date-and-timestamp-names-available-for-new-data-types).

Truncates a value (`<expression>`) of type `PGDATE`, `TIMESTAMPNTZ`, or `TIMESTAMPTZ` to the selected precision (`<time_unit>`).

{: .note}
The functions works with new `PGDATE`, `TIMESTAMPTZ`, and `TIMESTAMPNTZ` data types. If you are using legacy `DATE` and `TIMESTAMP` data types, see [DATE_TRUNC (legacy)](../functions-reference/date-trunc.md).

## Syntax
{: .no_toc}

```sql
DATE_TRUNC(<time_unit>, <expression> [, <time_zone> ])
```

## Parameters
{: .no_toc}

| Parameter   | Description                                            | Supported input types                                            |
| :---------- | :----------------------------------------------------- | ---------------------------------------------------------------- |
| `<time_unit>` | The time unit precision to truncate to.                           | `'microseconds'`, `'milliseconds'`, `'second'`, `'minute'`, `'hour'`, `'day'`, `'week'`, `'month'`, `'quarter'`, `'year'`, `'decade'`, `'century'`, `'millennium'`  (quoted)               |
| `<expression> `   | An expression to be truncated.                      | `PGDATE`, `TIMESTAMPNTZ`, `TIMESTAMPTZ`                          |
| `<time_zone>` | Optional, the time zone to use for `TIMESTAMPTZ` values. | A string literal such as `'Europe/Berlin'`                      |

## Return Types

Same as the input data type (`<time_unit>`)

## Remarks
{: .no_toc}

When the `<expression>` value is of type `TIMESTAMPTZ`, truncation is performed after converting the value from Unix time to local time in a particular time zone. For instance, truncation to 'day' produces a `TIMESTAMPTZ` that is midnight in that time zone.

By default, `DATE_TRUNC` uses the time zone specified in the session's `<time_zone>` setting, but the optional `<time_zone>` argument can be provided to use a different time zone.

Firebolt throws an error if the `<time_zone>` argument is provided for a source of type `PGDATE` or `TIMESTAMPNTZ`.

The `DATE_TRUNC` function can be used in the `PARTITION BY` clause of `CREATE TABLE` commands.

```sql
CREATE DIMENSION TABLE test (
  d PGDATE,
  t TIMESTAMPNTZ
)
PARTITION BY DATE_TRUNC('month', d), DATE_TRUNC('hour', t);
```

## Example
{: .no_toc}

```sql
SELECT DATE_TRUNC('century', PGDATE '1996-09-03');  --> 1901-01-01
SELECT DATE_TRUNC('hour', TIMESTAMPNTZ '1996-09-03 11:19:42.123');  --> 1996-09-03 11:00:00

SET time_zone = 'US/Pacific';
SELECT DATE_TRUNC('week', TIMESTAMPTZ '1996-09-03 11:19:42.123 Europe/Berlin');  --> 1996-09-02 00:00:00-07
SELECT DATE_TRUNC('week', TIMESTAMPTZ '1996-09-03 11:19:42.123 Europe/Berlin', 'Europe/Berlin');  --> 1996-09-01 15:00:00-07
```
