# Intl Locale Info API

## Draft Spec
https://tc39.github.io/proposal-intl-locale-info 
## Stage 
Stage 2

* Advanced to [Stage 1](https://docs.google.com/presentation/d/1NwYFb6jm5aQOiL9urMM-GaEFMhll5sYcZJYQ1WZhTZ8/edit#slide=id.p) in TC39 2020-09 meeting.
* Plan to propose to [advance to Stage 2](https://docs.google.com/presentation/d/1ct7h9pLHmXCwojGlReNjAT9RgysqLk_3lyUcllnOQYs/) in [TC39 2021-01-25~28 meeting](https://github.com/tc39/agendas/blob/master/2021/01.md).
  * Jan 2021 TC39 meeting approve to move to Stage 2 w/ the condiction to drop unitInfo

## Champion
* Frank Tang @FrankYFTang

## Scope

A proposal to expose Locale information, such as week data (first day in a week, weekend start day, weekend end  day, minimun day in the first week), hour cycle used in the locale, measurement system used in the locale.

* Week Data: (User request: https://github.com/tc39/ecma402/issues/6 )
  * Prior Arts: 
    * [weekstart](https://github.com/gamtiq/weekstart) JS Library to get first day of week. getWeekStartByRegion(RegionCode) / getWeekStartByLocale(locale);
    * [mozIntl.getCalendarInfo( locale )](https://firefox-source-docs.mozilla.org/intl/dataintl.html#mozintl-getcalendarinfo-locale)
    * ["date-fns/is_weekend" an non-i18n weekend javascript library](https://date-fns.org/v1.9.0/docs/isWeekend)
    * ['date-fns/start_of_week' an non-i18n weekend javascript library](https://date-fns.org/v1.29.0/docs/startOfWeek)
    * [JS code snippet of non-i18n solution on stackoverflow](https://stackoverflow.com/questions/3551795/how-to-determine-if-date-is-weekend-in-javascript)
    * ICU4J [Calendar.getWeekData()](https://unicode-org.github.io/icu-docs/apidoc/released/icu4j/com/ibm/icu/util/Calendar.html#getWeekData--) and [Calendar.WeekData](https://unicode-org.github.io/icu-docs/apidoc/released/icu4j/com/ibm/icu/util/Calendar.WeekData.html#minimalDaysInFirstWeek)
    * ICU [Calendar](https://unicode-org.github.io/icu-docs/apidoc/released/icu4c/classicu_1_1Calendar.html)
      * [Calendar::getFirstDayOfWeek](https://unicode-org.github.io/icu-docs/apidoc/released/icu4c/classicu_1_1Calendar.html#aa95d4e17ea169d0388a3a18489e67da0)
      * [Calendar::getMinimalDaysInFirstWeek](https://unicode-org.github.io/icu-docs/apidoc/released/icu4c/classicu_1_1Calendar.html#af10922ea91e4e4ccef6624ac3f18e621)
      * [Calendar::getDayOfWeekType](https://unicode-org.github.io/icu-docs/apidoc/released/icu4c/classicu_1_1Calendar.html#adc18b432f868737e115ece5f6e3c95ab)
* Hour Cycle
* Direction (User request: https://github.com/tc39/ecma402/issues/205 )
  * Prior Arts: 
    * [mozIntl.getLocaleInfo(locales, options)](https://firefox-source-docs.mozilla.org/intl/dataintl.html#mozintl-getlocaleinfo-locales-options)
    * [Closure goog.i18n.bidi.isRtlLanguage(language)](https://github.com/google/closure-library/blob/master/closure/goog/i18n/bidi.js)
    * [rtl-detect](https://www.npmjs.com/package/rtl-detect)
* Measurement System:
  * Prior Arts:
    * ICU4J [LocaleData.getMeasurementSystem](https://unicode-org.github.io/icu-docs/apidoc/released/icu4j/com/ibm/icu/util/LocaleData.html#getMeasurementSystem-com.ibm.icu.util.ULocale-) and [LocaleData.MeasurementSystem](https://unicode-org.github.io/icu-docs/apidoc/released/icu4j/com/ibm/icu/util/LocaleData.MeasurementSystem.html)
    * ICU4C [ulocdata_getMeasurementSystem](https://unicode-org.github.io/icu-docs/apidoc/released/icu4c/ulocdata_8h.html#a7abb69df19b1080b76fcc26ec0ea0978)

## Motivation / Use Case

Locale Information is necessary for many low level operations.

* Direction in textInfo will be needed for layout, and for localization information (bidi boundaries with placeholders etc.)
* WeekInfo will be needed for calendar widgets and applications to display correct monthly and weekly views.

## High Level Design

v8 prototype of Option A in https://chromium-review.googlesource.com/c/v8/v8/+/2570218

$ out/x64.release/d8 --harmony_intl_locale_info

Add methods to Intl to get object to contains group of information:
#### Week Data

```
let he = new Intl.Locale("he")
he.weekInfo
// {firstDay: 7, weekendStart: 5, weekendEnd: 6, minimalDays: 1}
let af = new Intl.Locale("af")
af.weekInfo
// {firstDay: 7, weekendStart: 6, weekendEnd: 7, minimalDays: 1}
enGB = new Intl.Locale("en-GB")
enGB.weekInfo
// {firstDay: 1, weekendStart: 6, weekendEnd: 7, minimalDays: 4}
```
Monday is 1 and Sunday is 7, as defined by ISO-8861 and followed by [Temporal proposal](https://tc39.es/proposal-temporal/#sec-temporal-todayofweek)
#### Text Information
```
l = new Intl.Locale("ar")
let textInfo = l.textInfo;
// { direction: "rtl" }
l.textInfo.direction
// rtl
```

#### ~Unit Information~

```
l = new Intl.Locale("ar")
let unitInfo = l.unitInfo;
// {measurementSystem: "metric"}
l.unitInfo.measurementSystem
// metric
l = new Intl.Locale("en-GB")
l.unitInfo
// {measurementSystem: "uksystem"}
l = new Intl.Locale("en-GB")
l.unitInfo
// {measurementSystem: "uksystem"}
l = new Intl.Locale("en")
l.unitInfo
// {measurementSystem: "ussystem"}

```

#### Defaults
```
l = new Intl.Locale("ja")
let defaults = l.defaults;
// { calendar: "gregory", hourCycle: "h23", commonCalendars: ["gregory", "japanes"] }
```


