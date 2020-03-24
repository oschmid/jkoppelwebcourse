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

1. There is hidden coupling in the format of the message footer. Changing the message format requires knowing each of the locations messages can be written. To eliminate that I would create a single message wrapper class (or function) to deduplicate writing message bytes to a stream.
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
      | Extra_headers = list of extra headers | Extra_headers = list of extra headers | |
      | Extra_headers = None | Extra_headers = {} | msg[‘Date’] is added with current date/time, msg[‘Message-ID’] is added, all other extra headers are added with existing casing except for ‘from’ |
   1. When reply_to = None, it should use a default value such as settings.DEFAULT_REPLY_TO_EMAIL (or reuse settings.DEFAULT_FROM_EMAIL). Adding reasons would make each list of emails become a list of tuples n=2, one part being the optional reason and the other being the email. message() would then also need to format these when joining them together.
   1. Headers are metadata about the email. They have a name and one or more values. Things like: who sent the email, who it was sent to, etc. Some are required, some are optional, some have default values, some are one-to-many, some are one-to-one.
      In code these are expressed as either a class member variable of type string or list of strings. Alternatively the header could be a member variable of `extra_headers`. Having 2 different places for headers to be defined as well as string and list of string headers makes this code complicated.
   1. To simplify we're going to have one `headers` dictionary as a member variable and accept fewer params in `__init__`. We're also going to use lowercase for all headers (RFC 2045).
      ```
      def __init__(self, headers=None, body='', connection=None, attachments=None):
         self.headers = headers or {} // though we should call lower() on all keys in headers
      
         // list headers like 'to' can be set like:
         to = self.headers['to']
         if to:
            if isinstance(to, str):
               raise TypeError('"to" argument must be a list or tuple')
            self.headers['to'] = list(to)
         else
            self.headers['to'] = []
         
         // single string headers like 'From' can be set like:
         self.headers['from'] = self.headers['from'] or settings.DEFAULT_FROM_EMAIL
         
      def message(self):
         // here we just need to join any list headers
         msg = //...
         for name, value in self.headers.items():
            if isinstance(value, str):
               msg[name] = value
            else
               msg[name] = ', '.join(str(v) for v in value
      
      // if we still want the convenience of being able to access the to and other headers as fields
      // we can add getters/setters:
      def to(self, to):
         if to:
            self.headers['to'] = to
         return self.headers['to']
      ```
1. Emails are strings for which `sanitize_address` is called in multiple places. [sanitize_address](https://github.com/django/django/blob/65e86948b80262574058a94ccaae3a9b59c3faea/django/core/mail/message.py) even converts it to a more specific `Address` type before converting it back to string.
1. There's no validation that 'Subject' is a string and not a list of strings. All of the basic headers can be defined in either a constructor argument or in the extra headers dictionary and result in the same email.
1. I would have kept all the existing constructor arguments and refactored only the internal structure thinking it a convenience rather than a confusion to have named arguments as well as a headers dictionary.

## Optional: Hidden Coupling Drill

1. 
   1. bar()
   1. Visible
   1. n/a
1. 
   1. 8 is coupled to the length of `str`
   1. Hidden
   1. It can be removed by replacing 8 with `strlen(str)`
1. 
   1. That the length of players is 2
   1. Hidden
   1. It can be removed by replacing `i < 2` with `i < game.players.length`
1. 
   1. That the `days` default value is the same as the comment
   1. Hidden
   1. The comment `# Do 90 days of history by default` can be removed
1. 
   1. Both classes are coupled to the name of the log file `"log.txt"`
   1. Hidden
   1. Each `"log.txt"` could be replaced with the same static variable `LOG_FILE`
1. 
   1. Client and server are coupled to the use of "username" and "password"
   1. Hidden
   1. Not possible to remove this for forms
1. 
   1. Both are coupled to the file path
   1. Hidden
   1. Could replace with `openFile(MONSTER1_GIF)` and `openFile(FIREBALL_GIF)`
1. 
   1. GraphicsProgram is coupled to the types of different shapes
   1. Hidden
   1. All shapes could inherit from a `Shape` class that has an abstract `getArea()` method
1. 
   1. The length and order of `recipeNames` and `recipeTypes` are coupled
   1. Hidden
   1. Replace both arrays with a `Recipe[]` where Recipe is a class that has name and type fields

### Mini-Case Study: The X Macro Trick (optional)

TODO
