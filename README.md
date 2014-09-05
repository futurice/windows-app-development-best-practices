win-client-dev-good-practices
=============================

A collection windows phone and windows 8 app development good practices from Futurice

----------------------------


THESE PRACTICES ARE AIMED FOR APPS THAT:
- Start up fast enough, present user with top of the line visual quality and fluidity (constantly good framerate) and let user navigate between pages (handling backstack and passing parameters from page to page), allow popups when required. 
- Get data from a backend
  - Multiple backends (with different data formats) might be used
  - Most likely json/xml from a restful web service
  - In most cases caching the data and falling back to the cached data when disconnected is required
    - Clearing / expiring the cache needs to be supported as well
  - The backend, it's APIs, or the format of the data returned might change. The architecture needs to be able to adapt with as little changes as possible
  - The APIs or the format provided might not be ideal for the app's object model. In many cases just mapping the data into model classes is not ideal. The architecture needs to be able to map any kind of backend into the object model that is ideal for the app.
- Parse the data into model objects and setup references between them
  - In some cases it is worthwhile to parse from the stream while the data is still being downloaded
  - In some cases model objects in memory need to be updated from the downloaded data. In these cases it is important that the updates get pushed to the UI.
  - In some cases it is worthwhile to aim for pushing the parsed objects (or their carried changes) into the UI ASAP, while the downloading and parsing might still be ongoing.
- Might have multiple consequent (interrelated?) requests ongoing
- Can show a progress indicator to the user while the backend request and parsing of the data is in progress
  - Ideally a progress bar
- Implement deep analytics to track both user actions and bugs

ADDITIONALLY THE APPS MIGHT:  
- Handle authentication
  - The requirements vary from just blocking pages from unauthenticated users to more finegrained access control to the data (possible already dowloaded into the app)
  - In some cases registration and login are handled by third parties, and third party implementations, such as facebook, web view navigated to a web page implemented by a third party etc..
  - In some cases login and registration UIs are implemented by the app, in which cases efficient form handling with validation, hints and such is important.
- Push new data or updates to the received data to the backend
  - In some cases it is required to be able to store the changes on the device between sessions

THESE REQUIREMENTS LEAD INTO THE FOLLOWING GUIDELINES:
- 

WHICH LEAD INTO THE FOLLOWING WP/WIN 8 ARCHITECHTURE (Patterns, frameworks, libraries):
-

BEST TOOLS FOR GIVEN WP/WIN 8 DEVELOPMENT TASK:
-
