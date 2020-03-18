## Dark Knowledge and Graph Grammars

Presented with only the end result the sequence of rewrites and why those decisions were made is hidden (i.e. dark).

You could capture it by recording all transformations as the set P. The ordering wouldn't matter as a tool like DxTer could still use them to recreate the end program given the starting program. In the 1D grammars we typically use however you can only captured that dark knowledge approximately using comments, source control, and feature spec documents.

## My favorite principle for code quality

Refactoring according to the Don't Repeat Yourself and Single Point of Truth principles will only result in collapsing the if statements like the version Bob and Charlie arrived at together.

## Optional: Boolean Blindness

TODO “example similar to pred where computing a boolean forces you to repeat a check later on”

## Design exercise: To Do List

https://gist.github.com/oschmid/3ee4c21525ef9082390ba469c897d7cf

## Case Study: Django Email Subsystem

1. There is hidden coupling in the format of the message footer. Changing the message format requires knowing each of the locations messages can be written. To eliminate that a single message wrapper class (or function) should be created to deduplicate writing message bytes to a stream.
1.
   1. try/except block with a conditional raise.
   1. TODO
   1. TODO Use “with self.open() as connection:”?
1.
   1. Filepath must be a string, a directory, exist already or be creatable, and be writable. The file-based backend was designed to check on initialization that it could read/write data to the file system. The file based backend also backs onto individual files for each message and doesn’t have the concept of a single stream like other backends.
   1. More backends could be developed that don’t have the concept of a single stream.
   1. TODO
1.
   1. | Header | __init__ | message() |
      |--------|----------|-----------|
      | To = list of email strings | To = list of email strings | msg[‘To’] = email strings joined with commas |
      | To = None | To = [] | msg[‘To’] = None |
      | extra_headers[‘To’] = string | To = [] | msg[‘To’] = extra_headers[‘To’] |
      | Cc (same as To) | | |
      | Bcc (same as To) | | |
      | Reply_to (same as To) | | |
      | From_email = string | From_email = string | msg[‘From’] = string |
      | From_email = None | From_email = settings.DEFAULT_FROM_EMAIL | msg[‘From’] = settings.DEFAULT_FROM_EMAIL |
      | extra_headers[‘From’] = string | From_email = settings.DEFAULT_FROM_EMAIL | msg[‘From’] = extra_headers[‘From’] |
      | Subject = string | Subject = string | Subject = string |
      | Subject = None | Subject = None | Subject = None |
      | Body = String | Body = String | Msg = SafeMIMEText |
      | Body = None | Body = “” | Msg = SafeMIMEText |
      | Attachments = list of objects | Attachments = list of file names, attach() is called on each | Msg has attachments attached |
      | Attachments = None | Attachments = [] | Msg has no attachments |
      | Extra_headers = list of extra headers | Extra_headers = list of extra headers | |
      | Extra_headers = None | Extra_headers = {} | msg[‘Date’] is added with current date/time, msg[‘Message-ID’] is added, all other extra headers are added with existing casing except for ‘from’ |
   1. When reply_to = None, it should use a default value such as settings.DEFAULT_REPLY_TO_EMAIL (or reuse settings.DEFAULT_FROM_EMAIL). Adding reasons would make each list of emails become a list of tuples n=2, one part being the optional reason and the other being the email. message() would then also need to format these when joining them together.
   1. Headers are metadata about the email. Who sent it, who it goes to, etc. Some are required, some are optional, some have default values, some are one-to-many, some are one-to-one. There is also added complication in the code from wanting to handle headers in 2 different ways, as named params and as an extra_headers object. Combining them as done currently spreads reading and formatting code across 2 methods (__init__ and message()).
   1. TODO
1. TODO
1. TODO
1. TODO
