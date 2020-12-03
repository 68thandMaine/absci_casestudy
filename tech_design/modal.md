# Modal and Resend Message Components

## [Return to main page](../README.md)

The modal component is used to show the resend message preview. While the `<Modal></Modal>` itself is provided through Material UI, the `ResendMessagePreview` component needed to be built.

```javascript
const ResendMessagePreview = React.forwardRef((props, ref) => {
 const { classes } = props;
 const [open, setOpen] = useState(false);
 
 const [visibility, setVisibility] = useState('visible');
 useEffect(() => {
  Object.keys(props.snackbarMessage).length === 0 ? setVisibility('visible') : setVisibility('hidden')
 }, [props.snackbarMessage]);


 function renderRecipients(recipientList) {
  return (
   <div className={classes.recipientInformationWrapper}>
    {recipientList.map((recipient, idx) => {
     return (
      <div key={idx} className={classes.resendMessagePreview__recipientInformation} onClick={() => setOpen(!open) }>
       <p>{recipient.name}</p>
       <p>{recipient.email}</p>
      </div>
     );
    })}
   </div>
   );
 }

 return (
  <Fragment>
   <Box visibility={visibility}>
    <Paper className={classes.resendMessagePreview}>
     <Grid container item direction="column">
      <Grid item>
       <Typography variant="h3">
        Resend Message
       </Typography>
       <Typography variant="subtitle1">
        This will resend the following message to
        <span className={classes.resendMessagePreview__guestCount} onClick={() => setOpen(!open)}>
         {props.data.recipients.length} of your guests.
        </span>
       </Typography>
      </Grid>
      <Grid item>
       <Collapse in={open}>
        {renderRecipients(props.data.recipients)}
       </Collapse>
      </Grid>
     </Grid>
     <Grid item >
      <Paper elevation={1} className={classes.resendMessagePreview__messageWrapper}>
       <MessagePreview data={props.data} />
      </Paper>
     </Grid>
     <Grid container item direction="column">
      <Grid item>
       <Typography variant="subtitle1">
        Are you sure you want to resend this message?
       </Typography>
      </Grid>
      <Grid item className={classes.resendMessagePreview__buttonWrapper}>
       <MainActionButton
        onClick={() => props.closeModal() }>No</MainActionButton>
       <MainActionButton
        onClick={() => props.resendMessage()}>Yes</MainActionButton>
      </Grid> 
     </Grid>
    </Paper>
   </Box>
   <SnackbarMessage
    type={props.snackbarMessage.type}
    message={props.snackbarMessage.message}
    allowUpdates />
  </Fragment>
 )
});
```

Most of the `ResendMessagePreview` component relies on Material UI to provide UI elements. Of particular note is the use of the `useState` and `useEffect` hooks. This allows the component to re-render if the sending a message succeeds or fails.
