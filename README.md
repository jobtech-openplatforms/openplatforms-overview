# Open Platforms
Open Platforms can be said to be two things. 

On the one hand Open Platforms is a set of ideas about gig platforms and gig workers and the data that are created on gig platforms. For more information about the concept of Open Platforms have a look here: [openplatforms.org](https://www.openplatforms.org/en/home)

On the other hand, and what is more relevant here, Open Platforms is a set of services that aims implements the concepts described above. The picture below describes the system from a high level.

![Functional overview over Open Platforms](https://github.com/jobtech-openplatforms/openplatforms-overview/blob/master/images/open-platforms-functional-overview.png)

# Current status
The system is in a clear beta state. The publicly deployed artifacts are not yet meant for unsupervised consumption. If you're interested in trying Open Platforms out, please let us know by opening an issue in this repo.

# The code
The code that makes up the entire system is located in a handful of reposes, located under the jobtech-openplatforms organization. Below follows a short description of those reposes.

## [gig-data-api](https://github.com/jobtech-openplatforms/gig-data-api)
This is where the code for the central system of Open Platforms resides. There are three units of deployable functionality:

* *Gigdata API -*
The API-endpoint can be found here: [gigdata-api.openplatforms.org](https://gigdata-api.openplatforms.org/). This is the api used by applications (data consumers) that want to integrate with Open Platforms.
* *Notification functions -*
Functionality for notifying relevant applications of changes in the data.
* *Platform Data fetcher -*
The background service that is responsible for scheduling and performing data fetches for the connected users on the connected platforms.

## [gig-platform-api](https://github.com/jobtech-openplatforms/gig-platform-api)
This repository contains code that deals with actually fetching data from out-in (see description below) integrated platforms and provide the interface used by applications and platforms for managing their integrations. Two deployable units can be found here:

* *Dev portal -*
The dev portal is an app where developers can register an account and start developing gig platform integrations and application integrations. The app can be found here: [dev.openplatforms.org](https://dev.openplatforms.org/)
* *Gig data API service-*
The Gigdata API service is responsible for talking to platforms that have integrated with the API to fetch data from them upon request.

## [mock-gig-platform](https://github.com/jobtech-openplatforms/mock-gig-platform)
A simple mock platform used for testing. It also functions as a reference implementation for integrators.

## [gig-data-common](https://github.com/jobtech-openplatforms/gig-data-common)
A library repo containing common functionality shared between projects.

## [user-portal](https://github.com/jobtech-openplatforms/user-portal)
User portal is an app where a user can disable active connections between apps and gig platforms and between Open Platforms and gig platforms. The app can be found here: [user.openplatforms.org](https://user.openplatforms.org/)

## [my-digital-backpack](https://github.com/jobtech-openplatforms/my-digital-backpack)
Not really a part of the Open Platforms implementation. It serves as a POC of an application (data-consumer) of the Open Platforms implementation. The app can be found here: [my-digital-backpack.web.app](https://my-digital-backpack.web.app/)

# Actors
In the Open Platforms universe there are three types of actors; users, platforms and applications. This section will describe each actor and their relation with the other types of actors.

## Platforms
Platforms are the fundamental building piece in the gig landscape. These are the actors that connect gig-workers with buyers. Examples of platforms are Freelancer.com, Uber, Yepster and Airbnb. Platforms have data about a gig user. Open Platforms makes it possible for the platforms to offer functionality where the user can move their data outside of that platform and make use of it somewhere else (on other gig platforms or on other services). In Open Platforms, platforms can be thought of as data suppliers.

## Applications
Applications are actors that integrate with Open Platforms in order to consume data from the platforms for one or more users and make use of that data in some (hopefully) interesting way. In Open Platforms, applications should be thought of as data consumers. A platform that wants to also consume data from other platforms needs to integrate both as a platform (for making its data available) and as an application (for consuming data from other platforms).

## Users
A user is the individual that has data on one or more platforms. The user can connect their account on one or more platforms to Open platforms. Having authorized Open platforms to retrieve data on the users behalf, the user can then authorize applications to consume that data. The user is the one who decides if Open Platform can access their data on a specific platform. The user then decides which applications may access that data. The user can revoke granted access, both to Open Platforms and to applications, at any given time.

# Platform integrations
Platforms are integrated into Open Platforms in one of two ways. 

## Inward-out integration
Either the platform already has an API that the basic requirements. In that case, if the platform is deemed as interesting enough, an integration can be done from the Open Platform side of things. This is done mainly as a way to gain some base functionality from the start.

## Outward-in integration
What we would like is for platforms to actively themselves integrate with Open Platforms. To enable this scenario, we have designed and implemented a flow that aims at making it possible for a platform to implement an integration with minimal effort. The way to integrate is to make use of our developer portal, create a platform within the system and implement an endpoint on their end that will reply in a defined way. So Open Platforms does not offer an API which can be integrated with. Instead we have defined a specification of an endpoint that should be implemented in the platform in order to integrate with Open Platforms.

# Authorization
Open Platform works with data on user level. We only fetch data from platforms for users that have granted us permission to fetch data on their behalf. To enable this, we use one of two mechanisms.

## OAuth2 authorization flow
OAuth2 contains standardized flows for authorizing somebody else (in this case Open Platforms) to act on one’s behalf (the users behalf). One of the requirements for implementing an inward-out integration is that the API and platform targeted supports an OAuth2 authorization flow. Upon completing such an authorization flow, Open Platforms obtains an access token that will be used to continuously make calls to the platforms API on behalf of the user that granted Open Platforms the permission to do so. 

## Email based authorization flow
During our research phase we discovered that smaller platforms often have some characteristics that made us think about how we could design a way for them to integrate with Open Platforms in a way that would be feasible for them. These smaller platforms often do not have a sufficient level of technical skill to make advanced adjustments to their platform. Authorization being a fairly advanced subject, we decided that we wanted to offer them a simpler way of handling authorization. As there is no free lunch, this simpler way has tradeoffs in terms of security. Also this mechanism forces platforms using it to trust Open Platforms. But given the upside (of enabling transport of a users data that would otherwise be stuck within the silo of the platform) and the nature of the data not being that sensitive, it was decided that it was worth offering this type of authorizing mechanism.

On a high level, the email based authorization flow works as follows:
1. The user makes a claim about having an account with a given email address.
2. Open Platforms sends a verification email to the given email address.
3. If the user has control over the email address, he/she clicks the link in the verification email.
4. If the verification link was clicked, it is deemed that the Open Platform user making the claim in step 1 indeed owns the email address that was claimed.
5. Open Platforms starts to fetch data from the platform for the given email-address. This is where the trust-part from the platform's side comes in. They trust that we only fetch data for accounts where we have verified ownership.

# Functional flows
There are various flows happening in the system. Below is an images describing some of these flows. More flows will be added over time.

![Various flows](https://github.com/jobtech-openplatforms/openplatforms-overview/blob/master/images/openplatform-flows-various-scenarios.png)
