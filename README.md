win-client-dev-good-practices
=============================

A collection windows phone and windows 8 app development good practices from Futurice

----------------------------


THESE PRACTICES ARE AIMED FOR APPS THAT:
- R1: Start up fast enough, present user with top of the line visual quality and fluidity (constantly good framerate) and let user navigate between pages (handling backstack and passing parameters from page to page), allow popups when required.
- R2: Support easy optimization for different screen resolutions and small differences in aspect ratios
- R3: Implement different GUIs and navigation structures for different devices (WP, Win 8, (Xbox One, iOS and Android with Xamarin))
- R4: Get data from a backend
  - R4.1: Multiple backends (with different data formats) might be used
  - R4.2: Most likely json/xml from a restful web service
  - R4.3: In most cases caching the data and falling back to the cached data when disconnected is required
    - R4.3.1: Clearing / expiring the cache needs to be supported as well
  - R4.4: The backend, it's APIs, or the format of the data returned might change. The architecture needs to be able to adapt with as little changes as possible
  - R4.5: The APIs or the format provided might not be ideal for the app's object model. In many cases just mapping the data into model classes is not ideal. The architecture needs to be able to map any kind of backend into the object model that is ideal for the app.
  - R4.6: Support development against wip APIs and in scenarios when the API can not be accessed
  - R4.7: Support debugging against rare, but know API responses
- Parse the data into model objects and setup references between them
  - In some cases it is worthwhile to parse from the stream while the data is still being downloaded
  - In some cases model objects in memory need to be updated from the downloaded data. In these cases it is important that the updates get pushed to the UI.
  - In some cases it is worthwhile to aim for pushing the parsed objects (or their carried changes) into the UI ASAP, while the downloading and parsing might still be ongoing.
- Might have multiple consequent (interrelated?) requests ongoing
- Can show a progress indicator to the user while the backend request and parsing of the data is in progress
  - Ideally a progress bar
- Implement deep analytics to track both user actions and bugs
- Can be efficiently developed and maintained by varying number of developers with varying leves of expertice and experience.
- Support rapid GUI tweaking iterations and fast build+deploy process to facilitate developer+designer 'pair programming'
- Support efficient unit (and integration) testing (black box? white box?) with mocking (only platform classes? all classes?).
- Support continous integration
- Can be realistically distributed via a 4G connection
- Have a memory footprint less than ~100 MB
- Have a disk space requirement of less than 2 GB
- Will be released as early as possible and greatly evolve during their lifespan with frequent updates delivered to the users
  - Update experience should be as smooth as possible with automatic data and settings migrations when necessary

ADDITIONALLY THE APPS MIGHT:  
- Handle authentication
  - The requirements vary from just blocking pages from unauthenticated users to more fine grained access control to the data (possible already dowloaded into the app)
  - In some cases registration and login are handled by third parties, and third party implementations, such as facebook, web view navigated to a web page implemented by a third party etc..
  - In some cases login and registration UIs are implemented by the app, in which cases efficient form handling with validation, hints and such is important.
- Push new data or updates to the received data to the backend
  - In some cases it is required to be able to store the changes on the device between sessions
- (Code up to view models might be required to be able to run on iOS and Android with Xamarin)

THE APPS ARE UNLIKELY TO:
- Have automated UI tests
- Require extremely low input or update latency throughout the app (game -like)
- Have a development team larger than three developers
- Be implemented as HTML-hybrid apps
- Be designed by the developer(s) themselves

THESE REQUIREMENTS LEAD INTO THE FOLLOWING GUIDELINES [Id / Responded requirements : Guideline]:
- G1 / R3, R4.4, R4.5: Follow strict separation of UI, bussiness logic, and data access
- G2 / R4.4, R4.5: Support easy utilization of bundled fake API responses
- Use the built in Visual Studio designer with design time data

WHICH LEAD INTO THE FOLLOWING WP/WIN 8 ARCHITECHTURE (Patterns, frameworks, libraries) [Implemented Guideline / Thing : (rationale)]:
- G1 / Follow MVVM and use MVVMCross

WHICH IS MOST EASILY IMPLEMENTED BY USING THE FOLLOWING TOOLS:
- Visual Studio 2013
- NuGet..
- ..
- 

SOLUTION TEMPLATE: ...
