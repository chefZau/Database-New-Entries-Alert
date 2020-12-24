# Email Alerts for New Entries on a Table

## Background

**Supervisor**: "Hey, Luca, our client wants to get email alerts for new user signup on their Web Store."

**Me**: "..Ok, I believe all the signup messages are in the ``Message`` table. So, I guess we can set up a tri.." (Yes, I want to say trigger)

**Supervisor**: "No, our client doesn't like the trigger on their database. If you can create a store procedure that would be great!"

**Me**: "...Ok, I'll look into it now. Is there any functional specification you want me to reach?"

**Supervisor**: "Yes... Your stored procedure need to check the Message table every five minutes, and then your algorithm needs to detect new entries and email a list of clients. We don't want any past data, right?"

**Supervisor**: "Do you have enough for you to start?"

**Me**: "Yes... I'll look into it now. Thanks, supervisor."

**Supervisor**: "Great, let me know if you need anything."

## Requirements

So... that is the story. Here are the technologies you need to have to follow along:

* SQL Server 2014 or above.
* SQL Server Management Studio (SSMS).

## Brainstorm

Our task is to email clients with new entries on a table. We might want to break that into two parts:

1. Find new entries on the table.
2. Email clients.

### Find New Entries on The Table

For your information, here is the schema for the table:

```sql
CREATE TABLE [dbo].[Message](
    [MessageID] [int] NOT NULL,
    [ToUserID] [int] NULL,
    [FromUserID] [int] NULL,
    [DateTimeSent] [smalldatetime] NOT NULL,
    [Subject] [varchar](200) NOT NULL,
    [Message] [varchar](2000) NULL,
    [MessageType] [tinyint] NOT NULL,
    [ObjectID] [int] NULL,
    [ObjectID2] [int] NULL,
    [ObjectID3] [int] NULL,
    [Actioned] [bit] NOT NULL,
    [MessageRead] [bit] NOT NULL,
    [MessageRuleID] [int] NULL,
    [SenderDeleted] [bit] NOT NULL,
    [ReceiverDeleted] [bit] NOT NULL,
CONSTRAINT [PK_Messages] PRIMARY KEY CLUSTERED
(
    [MessageID] ASC
)WITH (
    PAD_INDEX = OFF, 
    STATISTICS_NORECOMPUTE = OFF, 
    IGNORE_DUP_KEY = OFF, 
    ALLOW_ROW_LOCKS = ON, 
    ALLOW_PAGE_LOCKS = ON, 
    FILLFACTOR = 85
    ) ON [PRIMARY]
) ON [PRIMARY]
```

**Note**: the only things useful here are the MessageID and the Message columns. Let's run the `SELECT` statement below to get the rows.

```sql
SELECT TOP 2 MessageID, Message FROM [dbo].[Message];
```

Here is the output:

```text
MessageID	Message
112177	Request for access
    Requested login name: FAKE
    Customer Code/Account #: FAKE
    Customer Name: FAKE 
    Contact Number: xxxxxxxxx
    Email Address: FAKE@.ca
    Date/Time Requested: 7/31/2014 9:16:37 AM
111298	Request for access
    Requested login name: FAKE
    Customer Code/Account #: FAKE
    Customer Name: FAKE Brown
    Contact Number: xxxxxxxxx
    Email Address: FAKE@FAKE.com
    Date/Time Requested: 8/1/2014 1:31:00 PM
```

Fortunately, the message is structured. The columns are separete by a tab, and data ponint


### Email Clients