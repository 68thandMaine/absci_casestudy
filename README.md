# Private Porch: Development of the Marketing Hub and Guest Message

## Objective

Display data from emails generated by users, match styles, and identify any additional functionality based off invision assets.

## Background

Private Porch is a vacation rental platform that allows homeowners greater level of control over who they rent their vacation homes to. It creates a social network within the application, and is closed to public use. Users of Private Porch can join guest lists for different properties allowing the hosts the ability to communicate via email to their guests. The actual act of sending an email was handled through a third party service and the data that users entered is persisted in a database.

The 11th release of Private Porch included a feature which added a new page ([`MarketingHub`](#terms)) to the application. The acceptance criteria for the `MarketingHub` feature was loosely defined - most of the request was based off of an invision asset generated by a designer ([fig 1](#fig-1.-marketinghub)). Based off of the asset it was clear that new components for building a [`GuestMessage`](#terms) were needed and that a new route would need to be created to consume the correct email data in the database. Engineering the frontend to match the asset ultimately required backend work, but this is out of the scope of this case study. In short, routes were added to the [`api`](#terms), and several columns were added to the `guest_messages` table in the [`DB`](#terms).

## Tech Design

The first consideration for building this feature was how the [`client`](#terms) would get the data necessary to display [`GuestMessages`](#terms). Since content on the [`client`](#terms) is rendered through querying a Go api to return data needed to build HTML elements, the exact state for each view of the application needs to be fetched before each page loads. With this in mind, a component architecture becomes clear: use a container component to handle fetching specific data from the api, and several presentation components to support the UI.

### [Click here for more information on building the component architecture.](./tech_design/component_architecture.md)

After identifying how the application recieves data, the next step was to build the presentation components. Private Porch is a web application and despite it being geared towards desktop users, it will undoubtedly receive a fair amount of mobile traffic. Simply copying the design from [figure 1](#fig-1.-marketinghub) without considering how it would look on smaller screen sizes would be a costly mistake. The majority of the work behind the responsive design would be done on the [`GuestMessage`](#terms) component and resulted in [figure 2](#fig-2.-mobile-guestmessage).

### [Click here for more information on building the presentational components](./tech_design/presentation_components.md)

After designing a responsive layout for [`GuestMessages`](#terms) the last step to completing this feature would be adding behavior around the "Resend Email" button (see [fig 1](#fig-1.-marketinghub)). Again, no invision assets existed for this functionality. Implementing a modal seemed appropriate [figure 4](#fig-4.-resend).

### [Click here for more information on building the resend modal](./tech_design/modal.md)

## Hurdles

The largest hurdle that was encountered during development was due to a hasty decision to perform business logic on the client. The scenario is as follows:

**GIVEN** a user visits `/marketing`:  
**WHEN** the client requests all guest messages from the api,  
**THEN** the api returns all guest messages by user id.

Early on it became apparent that the payload from the API was inefficient for the needs of the project:

**GIVEN** a host sends one email to five guests:  
**WHEN** the host visits `/marketing`
**THEN** Five `GuestMessage` components with the same content render.

This unfortunate discovery was the result of how `GuestMessages` were saved to the database. Rather than creating a `users_guestMessages` table on the backend to handle guest messages with more than one recipient - every message sent was stored as a single row in one table. To solve for this issue a function, `reduceMessagesAndCountGuests()` was written:

```javascript
export function reduceMessagesAndCountGuests(messageList) {
 return messageList.reduce((messages, message) => {
  const messageCreationDate = message.createdAt.toString().substring(0,8);
  if(!messages[messageCreationDate]) {
   const propertyImage = (message.property.image != null) ? message.property.image.url : false;
   let msg = {
    id: messageCreationDate,
    message: message.message,
    subject: message.subject,
    sentAt: message.sentAt,
    propertyId: message.property.id,
    propertyPicture: propertyImage,
    recipients: [],
   }
   for(let i = 0; i < messageList.length; i+= 1) {
   if(message.property.id === messageList[i].property.id && messageCreationDate === messageList[i].createdAt.toString().substring(0,8)) {
    const recipientData = messageList[i].toUser;
    const recipient = {
      id: recipientData.id,
      name: recipientData.name,
      email: recipientData.email
     };
     msg.recipients.push(recipient);
   }
    messages[messageCreationDate] = msg;
   }
  }
  return messages;
 }, {})
}
```

This function takes the JSON payload of all `GuestMessages`, and creates a map with the following shape for each guest message:

```typescript
type GuestMessage = {
 [creation_date]: [{
  id: number;
  message: string;
  subject: string;
  sentAt: number;
  propertyId: number;
  propertyPicture: string;
  recipients: [{
   id: number;
   name: string;
   email: string;
  }]
 }]
}
```
> _Figure described with Typescript_

The data structure that is returned contains an array for the recipients, and values for the guest message itself. It is then returned to the `MarketingContainer` and passed to the `MarketingHubLayout` as props to render the Guest Message data.

## Limitations

- Frontend bloat
- Does not take advantage of websockets to return data to the client on server actions.
- Poor developer experience due to frontend containing confusing business logic.

___

## Terms

| Term | Definition |
| --- | --- |
| `Api` | Go API. |
| `Client` | Server side rendered React application. |
| `DB` | Postgres database. |
| `GuestMessage` | Record of an email that a host sends to guests on one of their guest lists. |
| `MarketingHub` | Page that Private Porch users use to interact with guests, and view a history of messages they have sent. |

## Figures

### Fig 1. `MarketingHub`

> ![invision_asset](./assets/invision_asset.png)

### Fig 2. Mobile `GuestMessage`

> ![mobile](./assets/mobile.png)

### Fig 3. Completed `MarketingHub`

> ![completedview](./assets/completedview.png)

### Fig 4 Resend Modal

> ![resend](./assets/resend.png)
