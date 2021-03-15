# Intl Locale Info API

## Draft Spec
https://tc39.github.io/proposal-intl-locale-info 
## Stage 
Stage 2

* Advanced to [Stage 1](https://docs.google.com/presentation/d/1NwYFb6jm5aQOiL9urMM-GaEFMhll5sYcZJYQ1WZhTZ8/edit#slide=id.p) in TC39 2020-09 meeting.
* Advanced to [Stage 2](https://docs.google.com/presentation/d/1ct7h9pLHmXCwojGlReNjAT9RgysqLk_3lyUcllnOQYs/) in [TC39 2021-01-25~28 meeting](https://github.com/tc39/agendas/blob/master/2021/01.md).
  * Jan 2021 TC39 meeting approve to move to Stage 2 **WITH the condiction to drop unitInfo**
* Plan to propose to [advance to Stage 3](https://docs.google.com/presentation/d/1h-iaDM5RiD5rpb0aYr1GMRLRRBh72zVEKtMyMJkCkfE/edit#slide=id.g98718d9573_0_37) in [TC39 2021-04 meeting](https://github.com/tc39/agendas/blob/master/2021/04.md).

### Entrance Criteria for Stage 1 (Proposal)
* Identified “champion” who will advance the addition: **Frank Yung-Fong Tang**
* Prose outlining the problem or need and the general shape of a solution: **See this document**
* Illustrative examples of usage: **See this document**
* High-level API: **See this document**
* Discussion of key algorithms, abstractions and semantics
* Identification of potential “cross-cutting” concerns and implementation challenges/complexity
* A publicly available repository for the proposal that captures the above requirements:
* 
### Entrance Criteria for Stage 2 (Draft)
* Above
* Initial spec text: DONE
* **Acceptance Signifies**: The committee expects the feature to be developed and eventually included in the standard

### Entrance Criteria for Stage 3 (Candidate)
* Above
* Complete spec text
* Designated reviewers have signed off on the current spec text
* All ECMAScript editors have signed off on the current spec text
* **Acceptance Signifies**: The solution is complete and no further work is possible without implementation experience, significant usage and external feedback.


## Champion
* Frank Tang @FrankYFTang

## Designated reviewers
* Shane Carr @sffc
* TBC

## ECMAScript editors
*

## Scope

A proposal to expose Locale information, such as week data (first day in a week, weekend start day, weekend end  day, minimun day in the first week), and text direction hour cycle used in the locale ~,measurement system used in the locale~.

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
* ~Hour Cycle~ DRIPPED by Champion
* Direction (User request: https://github.com/tc39/ecma402/issues/205 )
  * Prior Arts: 
    * [mozIntl.getLocaleInfo(locales, options)](https://firefox-source-docs.mozilla.org/intl/dataintl.html#mozintl-getlocaleinfo-locales-options)
    * [Closure goog.i18n.bidi.isRtlLanguage(language)](https://github.com/google/closure-library/blob/master/closure/goog/i18n/bidi.js)
    * [rtl-detect](https://www.npmjs.com/package/rtl-detect)
* ~Measurement System:~
  * ~Prior Arts:~
    * ~ICU4J [LocaleData.getMeasurementSystem](https://unicode-org.github.io/icu-docs/apidoc/released/icu4j/com/ibm/icu/util/LocaleData.html#getMeasurementSystem-com.ibm.icu.util.ULocale-) and [LocaleData.MeasurementSystem](https://unicode-org.github.io/icu-docs/apidoc/released/icu4j/com/ibm/icu/util/LocaleData.MeasurementSystem.html)~
    * ~ICU4C [ulocdata_getMeasurementSystem](https://unicode-org.github.io/icu-docs/apidoc/released/icu4c/ulocdata_8h.html#a7abb69df19b1080b76fcc26ec0ea0978)~

## Motivation / Use Case

Locale Information is necessary for many low level operations.

* Direction in textInfo will be needed for layout, and for localization information (bidi boundaries with placeholders etc.)
* WeekInfo will be needed for calendar widgets and applications to display correct monthly and weekly views.

## High Level Design

v8 prototype of Option A in https://chromium-review.googlesource.com/c/v8/v8/+/2570218

$ out/x64.release/d8 --harmony_intl_locale_info

Add methods to Intl to get object to contains group of information:
#### Week Data

```js
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
```js
l = new Intl.Locale("ar")
let textInfo = l.textInfo;
// { direction: "rtl" }
l.textInfo.direction
// rtl
```

#### Defaults
```js
~/v8/v8$ out/x64.release/d8 --harmony_intl_locale_info
V8 version 9.1.0 (candidate)
d8> ar = new Intl.Locale("ar")
ar
d8> ar.defaults
{calendars: ["gregory", "coptic", "islamic", "islamic-civil", "islamic-tbla"], collations: ["compat", "emoji", "eor"], hourCycles: ["h12"], numberingSystems: ["latn"]}
d8> arEG = new Intl.Locale("ar-EG")
ar-EG
d8> arEG.defaults
{calendars: ["gregory", "coptic", "islamic", "islamic-civil", "islamic-tbla"], collations: ["compat", "emoji", "eor"], hourCycles: ["h12"], numberingSystems: ["arab"], timeZones: ["Africa/Cairo"]}

d8> arSA = new Intl.Locale("ar-SA")
ar-SA
d8> arSA.defaults
{calendars: ["islamic-umalqura", "gregory", "islamic", "islamic-rgsa"], collations: ["compat", "emoji", "eor"], hourCycles: ["h12"], numberingSystems: ["arab"], timeZones: ["Asia/Riyadh"]}
d8> ja = new Intl.Locale("ja")
ja
d8> ja.defaults
{calendars: ["gregory", "japanese"], collations: ["unihan", "emoji", "eor"], hourCycles: ["h23"], numberingSystems: ["latn"]}
d8> jaJP = new Intl.Locale("ja-JP")
ja-JP
d8> jaJP.defaults
{calendars: ["gregory", "japanese"], collations: ["unihan", "emoji", "eor"], hourCycles: ["h23"], numberingSystems: ["latn"], timeZones: ["Asia/Tokyo"]}
d8> enUS = new Intl.Locale("en-US")
en-US
d8> enUS.defaults
{calendars: ["gregory"], collations: ["emoji", "eor"], hourCycles: ["h12"], numberingSystems: ["latn"], timeZones: ["America/Adak", "America/Anchorage", "America/Boise", "America/Chicago", "America/Denver", "America/Detroit", "America/Indiana/Knox", "America/Indiana/Marengo", "America/Indiana/Petersburg", "America/Indiana/Tell_City", "America/Indiana/Vevay", "America/Indiana/Vincennes", "America/Indiana/Winamac", "America/Indianapolis", "America/Juneau", "America/Kentucky/Monticello", "America/Los_Angeles", "America/Louisville", "America/Menominee", "America/Metlakatla", "America/New_York", "America/Nome", "America/North_Dakota/Beulah", "America/North_Dakota/Center", "America/North_Dakota/New_Salem", "America/Phoenix", "America/Sitka", "America/Yakutat", "Pacific/Honolulu"]}
d8> enNZ = new Intl.Locale("en-NZ")
en-NZ
d8> enNZ.defaults
{calendars: ["gregory"], collations: ["emoji", "eor"], hourCycles: ["h12"], numberingSystems: ["latn"], timeZones: ["Pacific/Auckland", "Pacific/Chatham"]}
d8> zh = new Intl.Locale("zh")
zh
d8> zh.defaults
{calendars: ["gregory", "chinese"], collations: ["pinyin", "big5han", "gb2312han", "stroke", "unihan", "zhuyin", "emoji", "eor"], hourCycles: ["h12"], numberingSystems: ["latn"]}
d8> zhTW = new Intl.Locale("zh-TW")      
zh-TW
d8>  zhTW.defaults
{calendars: ["gregory", "roc", "chinese"], collations: ["stroke", "big5han", "gb2312han", "pinyin", "unihan", "zhuyin", "emoji", "eor"], hourCycles: ["h12"], numberingSystems: ["latn"], timeZones: ["Asia/Taipei"]}
d8> zhHK = new Intl.Locale("zh-HK")
zh-HK
d8> zhHK.defaults
{calendars: ["gregory", "chinese"], collations: ["stroke", "big5han", "gb2312han", "pinyin", "unihan", "zhuyin", "emoji", "eor"], hourCycles: ["h12"], numberingSystems: ["latn"], timeZones: ["Asia/Hong_Kong"]}
d8> fa = new Intl.Locale("fa")
fa
d8> fa.defaults
{calendars: ["persian", "gregory", "islamic", "islamic-civil", "islamic-tbla"], collations: ["emoji", "eor"], hourCycles: ["h23"], numberingSystems: ["arabext"]}
d8> 

```

#### ~Unit Information~ DROPPED FEATURE

```js
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

