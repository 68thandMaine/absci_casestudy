# Presentation Components

The main presentation component that was built was for rendering `GuestMessages`.

```javascript
function GuestMessage(props) {
 const { classes, propertyPicture } = props;
 return (
  <ListItem className={classes.guestMessageCard} disableGutters>
    <Grid className={classes.guestMessageCard__messageContainer} container direction="row">
     <Grid container item>
      <Typography className={classes.guestMessageCard__subject} variant="h4">
       {props.subject}
       <Typography className={classes.guestMessageCardSubject__timeSent} variant="caption">
        {props.timeSent}
       </Typography>
      </Typography>
     </Grid>
     <Grid container item className={classes.guestMessageCard__infoContainer}>
      <Grid container item s={12} md={6} className={classes.infoContainer__statsWrapper}>
       <Grid item xs={6} className={classes.infoContainer__stats}>
        <Typography variant="subtitle2">Date Sent</Typography>
        <Typography>{props.dateSent}</Typography>
       </Grid>
       <Grid  item xs={6} className={classes.infoContainer__stats}>
        <Typography variant="subtitle2">Sent To</Typography>
        <Typography>{props.recipientCount} Guests</Typography>
       </Grid>
      </Grid>
      <Grid container item md={6} direction="column" className={classes.guestMessageCard__message}>
       <Typography variant="subtitle2">Message</Typography>
         <div
          className={classes.messagePreview}
          dangerouslySetInnerHTML={{
           __html: (props.message)
          }}
         />
       </Grid>
      </Grid>
     <Grid container item className={classes.guestMessageCard__resendEmailButton} >
       <MainActionButton  isFull onClick={() => props.previewMessage(props.id)}>Resend Email</MainActionButton>
      </Grid>
    </Grid>
    <Grid className={classes.guestMessageCard__propertyImage}>
     {!propertyPicture && (
      <div className={classes.noImagePropertyWrapper}>
       <ImageFilterHdr
        className={classes.noImageProperty}
       />
      </div>
     )}
     {propertyPicture && (
   <CardMedia className={classes.propertyImage} image={props.propertyPicture} />
     )}
    </Grid>
   </ListItem>
 )
}
```

The `GuestMessage` component relies on the [Material UI](https://material-ui.com/) API to provide pre-styled (but customizable) components.

| Component | Purpose |
| --- | --- |
| `<Grid></Grid>` | Renders a grid layout based on the props that are declared on it. Note the use of props like `md={6}`. These props are used for adjusting the style of the grid (row vs column ect) based on screen size.|
| `<Typography></Typography>` | Component that returns consistent, themed typography elements. |
| `<CardMedia></CardMedia>` | This component provides styles for images. |
