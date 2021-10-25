# Inactivity Timer - First Commit
## Global Variables
- The amount of inactive time will need to be accessed anywhere in the application.
- To accomplish this, use the Global.h file to store the variables for the time limit and the amount of inactive time that has passed.
```
#include <iostream>
#include <stdio.h>
#include <string>
#pragma once

class Global 
{
        public:
            std::string global_key;
            std::string global_iv;
            std::string changed_global_key;
            std::string changed_global_iv;
            // ****** TIMER ******
            int inactiveTime = 0;
            int timeLimit;
            // *******************
        public: 
            Global() = default;
            Global(const Global&) = delete;
            Global(Global&&) = delete;

            static Global& Instance()
            {
                static Global global;
                return global;
            }
};
```
# Manipulating the Global.h variables
- You can set/change the values in Global.h by writing functions in the ChangeGlobals.h header file.
- These can be accessed by instantiating ChangeGlobals and using the instance to execute the functions within the header file.
- If you use:
```
ChangeGlobals cg;
cg.functionName();
```
Then Make sure that class include the header file like:
```
#include "../Global/ChangeGlobals.h"
```
## Global Timer Functions
1. Function to set the timer
2. Function to reset the amount of inactive time back to 0 seconds
3. Function to increment the amount of inactive time by 1 (1 Second) 

Any file that includes the Global.h file allows you to access the variables by using the word "global".

```
void setTimer(){
  global.inactiveTime = global.inactiveTime + 1;
}

```

