# Google Summer of Code 2020 Final Report

**Student:** Hashir Sarwar  
**Organization**: Zulip  
**Mentor**: Hemanth V. Alluri

## Overview

My GSoC project was focused on extending features, fixing high priority bugs, and improving the overall experience of Zulip. This post serves as my final submission to the project.

Under this project there are three important areas covered:

- Push notifications and mobile features that require server-side changes
- Zulip frontend on mobile and desktop browsers
- Inline previews and markdown support

## Description

Following is the chronological detail of the work done during my GSoC period.

### Added support for iOS notification count

Previously, Zulip server didn't support to send notification count to iOS devices. I have worked to add this support along with automated testing.

*Related PR*  
Add support for setting counts in iOS push notifications [#15179](https://github.com/zulip/zulip/pull/15179)

### Added end-to-end encryption for push notifications

This is one of the most important features that I have worked on during my GSoC period. End-to-end encryption is a vital security enhancement especially for team chat software like Zulip.

I have used the AES-GCM algorithm to encrypt the data before sending it from the server to the mobile devices.

*Related PRs*  
Store tokens locally even when bouncer is used [#15261](https://github.com/zulip/zulip/pull/15261) (Preparatory work)  
Add end-to-end encryption for push notifications [#15229](https://github.com/zulip/zulip/pull/15229) (Actual work)

### Reduced Zulip's initial loading time

The size of the payload sent from the Zulip server to the browsers and mobile apps has been significantly reduced. To be more specific, the network bandwidth is reduced from 1.3MB compressed and 10.4MB total to 0.92MB compressed and 8.8MB total.

This has been made possible by not sending avatar URLs of long term idle users from the server. This field is not compressible, thus it eats up a lot of bandwidth. If we ever require an avatar URL of any long term idle user, we'll compute it from the frontend/mobile app.

*Related PRs*  
Avoid sending avatar URLs of long term idle users to clients [#15359](https://github.com/zulip/zulip/pull/15359)  
Add user_avatar_url_field_optional support to the web client [#15546](https://github.com/zulip/zulip/pull/15546)

### Investigated the slow performance on Chrome mobile browser

Zulip on Chrome mobile browser is seriously slow; particularly the scrolling is not smooth. I have investigated, posted details, and proposed some fixes for laggy scrolling on Chrome mobile.

It turned out that Chrome is creating multiple composite layers when Zulip is being run on mobile which is causing layer invalidation and long "update layer tree" operations every time we scroll.

I have not opened any PR to fix this since it mostly involved refactoring HTML/CSS code and I have mostly worked on Python/JavaScript code in Zulip but I am sure my findings will be helpful to fix this in future.

### Improved experience for interacting with deactivated users

Many of the cases for deactivated users were left unhandled causing unexpected behaviors when a user interacts with deactivated users. The discussions concluded the following important cases that were not handled previously:

- Avoid PM list to collapse mysteriously when a deactivated user is clicked on.
- Allow deactivated users' pills in the compose box but distinguish them visually from other pills.
- Display a message that the user you are trying to interact is deactivated.

I have worked to handle all of these cases and add unit tests for each case.

![image](https://user-images.githubusercontent.com/45683359/88746327-bcf07a00-d165-11ea-9afe-5c76de380be1.png)

*Related PR*  
Fix behaviour of PM list with deactivated user [#13795](https://github.com/zulip/zulip/pull/13795)

### Fixed an unexpected typeahead behavior

Typeahead in the navbar didn't close unless an option is selected from the typeahead itself. The code causing this bug was investigated and this was addressed in my PR.

*Related PR*  
Fix binding of event handler to blur event [#15945](https://github.com/zulip/zulip/pull/15945)

### Fixed the "Mixed content" warning when URL previews displayed are from an unencrypted site

Browsers display a "mixed content" warning when initial HTML is loaded over a secure HTTPS connection, but other resources (such as images, videos, stylesheets, scripts) are loaded over an insecure HTTP connection. In this case, the preview images from unencrypted sites caused this warning. I addressed this by using the "thumbor" library to serve these images. The use of thumbor served two purposes:

- Serving all image content over HTTPS, even if the original image was hosted on HTTP. This is important to avoid mixed-content warnings from browsers and does have some real security benefits in protecting users from malicious content.
- Minimizing potentially unnecessary bandwidth that might be used in communication between the Zulip server and clients. Uploading large photos could result in a bad experience for users with a slow network connection.

*Related PR*  
Use thumbor for inline URL preview images [#16037](https://github.com/zulip/zulip/pull/16037)

### Added support for new markdown syntax `![title](url)`

Previously, Zulip didn't support the `![title](url)` syntax that allows sending images without any text link. I have adjusted the Zulip's existing model to make use of this markdown syntax and tweaked inline images' CSS properties accordingly. The added advantage is that this would give more control to the user for positioning of previews within a message.

*Related PR*  
markdown: Add support for new syntax `![title](url)`. [#16117](https://github.com/zulip/zulip/pull/16117)

### Add option to remove preview for a specific URL in a message

The idea was to provide an option to the user to remove any undesired URL preview from a sent message. One of the Zulip's maintainers has worked on this previously but his PR was stale and outdated. So, I have reworked on this with the required updates.

*Related PRs*  
Remove preview for a specific URL in a message [#16231](https://github.com/zulip/zulip/pull/16231)

### Store feature_level obtained from server in Redux

The purpose of `feature_level` is to provide a way for (non-webapp) clients, like the mobile and terminal apps, to tell whether the server it's talking to is new enough to support a given API feature -- in particular a way that

- is finer-grained than release numbers, so that for features developed after e.g. 2.1.0 we can use them immediately on servers deployed from master (like chat.zulip.org and zulipchat.com) without waiting the months until a 2.2 release;
- is reliable, unlike e.g. looking at the number of commits since a release;
- doesn't lead to a growing bag of named feature flags which the server has to go on sending forever.

I had started this project before my official GSoC period and continued working on this parallel to other projects.

*Related PR*  
Store server feature level in Redux [#4079](https://github.com/zulip/zulip-mobile/pull/4079)

## Final words

It has been an honor to be a part of one of the leading open source communities. My next plan is to continue working on my open PRs as per the directions of the reviewers and get them merged. Furthermore, I will keep contributing within my specialized domain of the project to stay an active part of this amazing open source community.

Lastly, huge thanks to Google and Zulip for providing the opportunity and my mentor Hemanth for guiding me throughout the program.
