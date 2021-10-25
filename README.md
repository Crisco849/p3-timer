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
## Manipulating the Global.h variables (ChangeGlobals.h)
- You can set/change the values in Global.h by writing functions in the ChangeGlobals.h header file.
- These can be accessed from other classes by instantiating ChangeGlobals and using the instance to execute the functions within the header file.
- If you use:
```
ChangeGlobals cg;
cg.functionName();
```
## Global Timer Functions You Need To Write
1. Function to set the timer ( void setTimer(int seconds)  )
2. Function to reset the amount of inactive time back to 0 seconds   (  void timerReset()   )
3. Function to increment the amount of inactive time by 1 (1 Second)    ( void incrementTimer() )

Any file that includes the Global.h file allows you to access the variables by using the word "global".

```
// Example from ChangeGlobals.h
    void yourTimerFunction(){
        global.inactiveTime = <your code>
    }
```
# Implementing functions to detect inactivity
You will be implementing the same functions and declaring the same header variable and functions to detect inactivity for three classes:
1. The Credential Vault (CredentialMenu.cpp)
2. Settings Menu (Settings.cpp)
3. Password Generator 
The Password Generator Class is not implemented yet, but it will be soon.
## Header files
- Public variables: define a QPoint variable. Stores (x,y) point of mouse.
```
QT_BEGIN_NAMESPACE
class CredentialMenu : public QMainWindow
{
	Q_OBJECT
	public:
		CredentialMenu(QFrame *parent = 0);
		bool eventFilter(QObject *obj, QEvent *event);
		bool inImportExportFrame = false;
		void formatFrame(QFrame *obj);
		void formatTable(QTableWidget *table);
		void formatButtonWithinFrame(QPushButton *button, int originalFrameWidth, int originalFrameLength, QFrame *frame);
		void formatLineEditWithinFrame(QLineEdit *line, int originalFrameWidth, int originalFrameLength, QFrame *frame);
		void search(std::string searchTerm);
		std::string toLowerCase(std::string words);
		void continueExport();

		// ***** TIMER Variable ******
		QPoint cursorPosition;
		// **************************
```
- public slots functions: void functions for checking activity and updating the cursor location.
```
public slots:
		void openAddCredentialPrompt();
		void closeAddCredentialPrompt();
		void addCredential();
		void openSettings();
		void copySelectedCell();
		void removeSelectedCredential();
		void exportSelectedCredentials();
		void loginChangeData(QString hashedPass);

		// ****** TIMER SLOT Functions *******
		void checkActivity();
		void updateCursor();
		// **********************************
```
### CPP Class Files
Make sure that each class includes the two header files:
```
#include "../Global/ChangeGlobals.h"
#include "../Global/Global.h"
```
## Constructor Code
Each Class for those GUI screens has constructors that look like:
```
ExampleClass::ExampleClass(QFrame *parent)
    : QMainWindow(parent)
{
        ...
        ...
}
```
Towards the bottom of those functions will be connect functions that look something like: 
```
        connect(ui.RemoveButton, SIGNAL(clicked()), this, SLOT(removeSelectedCredential()));
	connect(ui.ExportSelectedButton, SIGNAL(clicked()), this, SLOT(exportSelectedCredentials()));
```
You will need to add connect functions for two timers that allow a SLOT function to be triggered after a specified amount of time passes.
The function that looks to see if there is inactivity will be called every second.
Part of that function is looking to see if the mouse is idle on the screen.
It does this by checking to see if the current cursor location is in the same place as where it was when it was last updated.
That cursor location gets updated every 0.5 to leave room for detection. This is done with a second timer.
```
        // Triggers the slot function every second this CredentialMenu window is open.
	QTimer *timer = new QTimer(this);
	connect(timer, SIGNAL(timeout()), this, SLOT(checkActivity()));
	timer->start(1000);

	// Updates where the cursor is every 0.5 seconds
	QTimer *updateCursorTimer = new QTimer(this);
	connect(updateCursorTimer, SIGNAL(timeout()), this, SLOT(updateCursor()));
	updateCursorTimer->start(500);

	// Stores initial position of cursor
	cursorPosition = QCursor::pos();
```
Then you're done with constructor code.

## Writing Slot Functions
Add these two functions to each class
```
/* Inactivity will be detected in two ways.
 * 1. If the mouse is not on the window.
 * 2. If the mouse is on the window, but not moving.
 * If either of these conditions is met in the checkActivity function which is called once every second,
 * then the global.inactiveTime variable will be incremented by 1 (1 second).
 * If checkActivity() is called and it finds that the amount of inactive time is equal to the time limit,
 * the application will close.
*/
void ClassName::updateCursor(){
	cursorPosition = QCursor::pos();
}

void ClassName::checkActivity(){
	ChangeGlobals cg;
	// Checks if mouse is on the window
	if(rect().contains(mapFromGlobal(QCursor::pos()))){
		// Checks if the mouse is idle in place
		if(QCursor::pos().x() == cursorPosition.x() && QCursor::pos().y() == cursorPosition.y()){
			// use the ChangeGlobals instance to call the function to increment the amount of inactive time.
		} else {
			// use the ChangeGlobals instance to call the function to reset the amount of inactive time back to 0 seconds
		}
	} else {
		// use the ChangeGlobals instance to call the function to increment the amount of inactive time.
	}

        // Quit the application once the amount of inactive time from the global header is equal to the timer limit in the global header
	if( global.<$(variable name for inactive time)> >= global.<$(variable name for time limit)> ){
		QCoreApplication::quit();
	}
}
```




