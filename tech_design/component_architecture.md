# `MarketingHub` Component Architecture

## [Return to main page](../README.md)

To display the `MarketingHub` a container component called [`MarketingContainer`](#marketingcontainer.jsx) will be built. This component is built to hold state value for the `GuestMessages`, and displaying a modal. Future development on the `MarketingContainer` that adds additional content to the page will require some sort of state to hold values. The `MarketingContainer1` component is set up uperfectly to continue to accept data and pass it to the children.

The `MarketingContainer` renders the [`MarketingHubLayout`](#marketinghublayout.jsx). This component is purely presentational and holds no stateful logic. It renders the `GuestMessage` component for each `GuestMessage` that it receives.

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
