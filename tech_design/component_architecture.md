# `MarketingHub` Component Architecture

## [Return to main page](../README.md)

The container component for this feature was named the `MarketingContainer`, and is built to hold state for the `GuestMessages` and to display a modal. It was important to design the `MarketingContainer` with uture development in mind. Private Porch is still being built and additional content will be added to the `MarketingHub` page which will require the `MarketingContainer` component to fetch new data, and pass it to additional child components. The `MarketingContainer1` component is set up perfectly to continue to accept data and pass it to the children because it passes values to the [`MarketingHubLayout`](#marketinghublayout.jsx) component. This component is purely presentational and holds no stateful logic, but it can render anything with a few style adjustments. Currently it renders `GuestMessage` components for each `GuestMessage` that it receives.

___

## Code Snippets

### MarketingContainer.jsx

```javascript
return (
<Fragment>
  <MarketingHubLayout
  auth={props.auth}
  hasFetched={props.hasFetched}
  isFetching={props.isFetching}
  messagesToGuests={listOfMessages}
  openModal={handleOpenModal}
  />
 <Modal
   open={open}
   aria-labelledby="simple-modal-title"
   aria-describedby="simple-modal-description"
 >
 <ResendMessagePreview
   ref={ref}
   data={previewMessage}
   resendMessage={handleResendMessage}
   snackbarMessage={snackbarContent}
   closeModal={resetContainerState}
 />
 </Modal>
</Fragment>
)
```

### MarketingHubLayout.jsx

```javascript
function MarketingHubLayout(props) {
 const { classes, messagesToGuests } =  props;
 function renderGuestMessages(msg) {
 return (
  <GuestMessage
   key={msg.id}
   id={msg.id}
   message={msg.message}
   subject={msg.subject}
   dateSent={getFormattedDate(msg.sentAt).date}
   timeSent={getFormattedDate(msg.sentAt).time}
   propertyPicture={msg.propertyPicture}
   recipientCount={msg.recipients.length}
   previewMessage={props.openModal}
  />
 )
}
 
return (
 <div className={classes.marketingHubLayoutContainer}>
  <div className={classes.marketingHubLayoutContainer__header}>
   <p className={classes.title}>Messages</p>
   <MainActionButton className={classes.messageGuestsButton} to={"/message-guests"}>
    <ChatIcon className={classes.messageGuestButton__icon}/>
    {"Message Guests"}
   </MainActionButton>
  </div>
  <div className={classes.marketingHubLayoutContainer__messages}>
   <List>
    {
    Object.values(messagesToGuests)
     .sort((a, b) => b.sentAt - a.sentAt)
     .map((message) => renderGuestMessages((message)) )
    }
   </List> 
  </div>
 </div>
 )
}
```

As new features are added to the `MarketingHub` the only adjustments that will need to be made are to the returned JSX of the `MarketingHubLayout`.
