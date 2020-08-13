# Intl Locale Info API


## Stage 
Stage 0

## Champion
* Frank Tang @FrankYFTang

## Scope

A proposal to expose Locale information, such as week data (first day in a week, weekend start day, weekend end  day, minimun day in the first week), hour cycle used in the locale, measurement system used in the locale.

* Week Data: (User request: https://github.com/tc39/ecma402/issues/6 )
  * Prior Arts: 
    * [mozIntl.getCalendarInfo( locale )](https://firefox-source-docs.mozilla.org/intl/dataintl.html#mozintl-getcalendarinfo-locale)
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
* Measurement System:
  * Prior Arts:
    * ICU4J [LocaleData.getMeasurementSystem](https://unicode-org.github.io/icu-docs/apidoc/released/icu4j/com/ibm/icu/util/LocaleData.html#getMeasurementSystem-com.ibm.icu.util.ULocale-) and [LocaleData.MeasurementSystem](https://unicode-org.github.io/icu-docs/apidoc/released/icu4j/com/ibm/icu/util/LocaleData.MeasurementSystem.html)
    * ICU4C [ulocdata_getMeasurementSystem](https://unicode-org.github.io/icu-docs/apidoc/released/icu4c/ulocdata_8h.html#a7abb69df19b1080b76fcc26ec0ea0978)

## High Level Design
### Option 1
Add getter to Intl.Locale for each value:

```
+ get Intl.Locale.prototype.firstDayOfWeek
+ get Intl.Locale.prototype.minimalDaysInFirstWeek
+ get Intl.Locale.prototype.weekendStart
+ get Intl.Locale.prototype.weekendEnd
+ get Intl.Locale.prototype.direction
+ get Intl.Locale.prototype.measurementSystem
+ get Intl.Locale.prototype.defaultHourCycle
+ get Intl.Locale.prototype.defaultCalendar
+ get Intl.Locale.prototype.commonCalendars

```
### Option 2
Add methods to Intl to get object to contains group of information:
#### Week Data
```
let weekInfo = Intl.weekInfo("en-US")
// { 
//  locale: "en-US", 
//  firstDayOfWeek: 7,
//  minimalDaysInFirstWeek: 4, 
//  weekendStart:  6,
//  weekendEnd: 7,
// }
```
Monday is 1 and Sunday is 7, as defined by ISO-8861 and followed by [Temporal proposal](https://tc39.es/proposal-temporal/#sec-temporal-todayofweek)
#### Text Information
```
let textInfo = Intl.textInfo("ar")
// { direction: "rtl" }
```
#### Defaults
```
let defaults = Intl.defaults("ja")
// { calendar: "gregory", hourCycle: "h23", commonCalendars: ["gregory", "japanes"] }
```
#### Unit Information
```
let unitInfo = Intl.unitInfo("ar")
// { measurementSystem: "US" }
```

# TO BE DELETED- FROM TEMPLATE
## Before creating a proposal

Please ensure the following:
  1. You have read the [process document](https://tc39.github.io/process-document/)
  1. You have reviewed the [existing proposals](https://github.com/tc39/proposals/)
  1. You are aware that your proposal requires being a member of TC39, or locating a TC39 delegate to "champion" your proposal

## Create your proposal repo

Follow these steps:
  1.  Click the green ["use this template"](https://github.com/tc39/template-for-proposals/generate) button in the repo header. (Note: Do not fork this repo in GitHub's web interface, as that will later prevent transfer into the TC39 organization)
  1.  Go to your repo settings “Options” page, under “GitHub Pages”, and set the source to the **main branch** under the root (and click Save, if it does not autosave this setting)
      1. check "Enforce HTTPS"
      1. On "Options", under "Features", Ensure "Issues" is checked, and disable "Wiki", and "Projects" (unless you intend to use Projects)
      1. Under "Merge button", check "automatically delete head branches"
<!--
  1.  Avoid merge conflicts with build process output files by running:
      ```sh
      git config --local --add merge.output.driver true
      git config --local --add merge.output.driver true
      ```
  1.  Add a post-rewrite git hook to auto-rebuild the output on every commit:
      ```sh
      cp hooks/post-rewrite .git/hooks/post-rewrite
      chmod +x .git/hooks/post-rewrite
      ```
-->
  1.  ["How to write a good explainer"][explainer] explains how to make a good first impression.

      > Each TC39 proposal should have a `README.md` file which explains the purpose
      > of the proposal and its shape at a high level.
      >
      > ...
      >
      > The rest of this page can be used as a template ...

      Your explainer can point readers to the `index.html` generated from `spec.emu`
      via markdown like

      ```markdown
      You can browse the [ecmarkup output](https://ACCOUNT.github.io/PROJECT/)
      or browse the [source](https://github.com/ACCOUNT/PROJECT/blob/HEAD/spec.emu).
      ```

      where *ACCOUNT* and *PROJECT* are the first two path elements in your project's Github URL.
      For example, for github.com/**tc39**/**template-for-proposals**, *ACCOUNT* is "tc39"
      and *PROJECT* is "template-for-proposals".


## Maintain your proposal repo

  1. Make your changes to `spec.emu` (ecmarkup uses HTML syntax, but is not HTML, so I strongly suggest not naming it ".html")
  1. Any commit that makes meaningful changes to the spec, should run `npm run build` and commit the resulting output.
  1. Whenever you update `ecmarkup`, run `npm run build` and commit any changes that come from that dependency.

  [explainer]: https://github.com/tc39/how-we-work/blob/HEAD/explainer.md
