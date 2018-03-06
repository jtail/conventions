### Logging

#### Assumptions

This section is compiled based on the following assumptions about logging purpose:

1. Assess overall state of the system at a given moment. 
If something was broken at 04:12 and went back to normal at 5:17 a glance at the logs should reveal something in that time interval. 

2. Investigate production incidents. 
If there is incoming information from a user that something went wrong with a specific business process, it should be possible to quickly verify the claim given some input identifier and find some clue on what stage of processing was broken. For example, given an order id that did not went through, it should be possible to find some clue on what stage of processing was broken.
  
3. Input for compensatory action.
If a process problem is being logged and compensatory action is generally possible for a given business scenario, enough information should be logged to be provided as input for such compensatory action. For example, if some orders were not processed, enough information must be logged for business can find those orders later and decide what to do with them - even if this action would be as simple as sending an apology letter.

4. Input to watchdog system.


#### General Practices

##### Log meaningful information from context
Relevant information from context must be included, especially when logging errors. For example, when logging a number parsing failure, it is relevant to log the string that failed to parse. When initializing a database connection, logging information such as url and username is also a great idea.
 
Motto: Problems investigated from production logs are usually hard to reproduce and ability to quickly identify the nature of problem by just looking at the log is priceless.

Note: If you find yourself in position when you need to log, but no meaningful information is available on context, this is a pretty strong code smell. Most likely, this is the wrong place to log, abstraction leak is present or not enough information is carried in the exceptions being thrown.

##### One log line per event
This ensures ability to quickly count the number of errors using simple tools, such as grep in a given log fragment. If more lines need to be printed, these should be logged as WARN after the error.

##### Log or throw, but not both
If you are throwing an exception, nothing should be logged above DEBUG level. Any information that might be of interest for investigation should go into the exception itself. If some information needs to be added on rethrowing, create a new exception wrapping the original one. 

##### When logging arbitrary strings, always surround them by marker characters. 
The recommended default marker is square bracket. Compared both to single and double quotes, they do not require escaping and are less often encountered in real data than all sorts of quotes (think about names and text with quotations).   

    // AVOID
    log.debug("Updating user {}", user.getId()); 
    // PREFER
    log.debug("Updating user [{}]", user.getId()); 

Motto: quickly identify issues with leading and trailing spaces in strings received from ouside.      

Note: This rule is mandatory only towards arbitrary strings. Strictly speaking it is not useful to surround an integer value or a class name being logged. However, during code review, it is diffucult to spot the difference between a method that returns a string id and a method that returns integer id and markers are harmless, so using them consistently for all logging is recognized as best practice. 

##### Stacktraces must be logged for unexpected exceptions
However it is recommended to avoid them when logging try-catching errors in a single invokation into a library code which is not expected to call back. For example, a stacktrace from a REST library, such as Jersey, will usually not contain any more useful information than error message itself. If caught exception is a 'wrapper', consider unwrapping it before logging to reduce the size of stacktrace. Also, consider logging stacktraces at a level lower than the original message.
    
##### Avoid logging sensitive data
Complete credit card numbers, passwords and similar sensitive data should never be written to logs.
Motto: In modern production environments, logs are often carried to external archive system where managing and tracking access to sensitive data is insufficient or nonexistent. 


#### Logging Levels

##### FATAL
Reserved to log a reason for imminient service termination. 
Multiplicity: just once. If you need more lines printed, use ERROR level to log it before spitting out a single last FATAL message. 

##### ERROR
Events that indicate a disruption, that requires immediate action. Somebody has to wake up in the middle of the night and do something.
Multiplicity: 1 line per event. 

##### WARN
A bad symptom, however not that significant to wake up in the middle of the night. The following situations explicitly belong to this level:
- An event that might indicate error-severe disruption of service, but does not neccessarily do so.    
- An event that might be pretty mundane on rare occassions, but a high repetition rate would indicate an error.
Multiplicity: 1 line per event. It is a good idea to think that 10 warnings are equivalent to 1 error, with all the relevant consequences (including waking somebody up in the middle of the night), so if something is routinely failing but does not disrupt 'business as usual', it should be INFO instead. 
It should be expected that messages logged at this level are examined on a daily basis by on-duty IT support engineer, armed with simple tools such as 'grep' and 'awk'. 

##### INFO
Essential information about normal operation. This is the level that should be set on production servers during normal operation, therefore logging output should contain enough information to fullfill assumptions 1, 2 and 3. To achieve that, logged information should be meaningful  

Multiplicity: 
For systems that are invoked by external triggers, such as incoming messages or HTTP requests, a small finite number of log records should appear per external trigger at INFO level. This, in turn, leads to the following cases: 
- INFO should never logged inside a loop. 
- A method, that might be invoked in a loop (for example a parsing function), should also never log INFO. 
- Code that is not likely to be invoked more than once per request (imagine user authorization), can log INFO messages.
- Instance utility classes, such as HTTP filters, should avoid logging at INFO level except for cases, when information being logged would significantly contribute to investigate incidents. For example, authorizing a request based on a cookie, should be DEBUG, however authorizing user in the system is worth INFO.

For bulk processing scenarios where individual items can fail independently, apply the same rules, but consider each item as an independent trigger. For example when importing a bulk file it is perfectly normal to have a record per item in file written. 

##### DEBUG
Anything required to troubleshoot the problem that is not reproducible in model conditions. However, avoid logging items of arbitrary lengths, such as raw responses from external systems. 
The output at this level should not be overwhelming, it should be possible to:
- Selectively enabling DEBUG output for any package should not cause performance degradation under production level loads. 
- Globally enabling DEBUG output under production level load should not bury the server under permanent 100% CPU, however some performance degradation is acceptable. 
(Of course we are speaking about logging level for application classes here, not for libraries) 

##### TRACE
Absolutely anything, including raw data. It is recommended, but not required to maintain the volume of output small enought to keep the possibility to enable this level for a single package under production without overwhelming the server (however performance degradation is acceptable).