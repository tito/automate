# Automate

Kivy automation, currently used to automate screenshots on various devices at
specific places.

# Installation

    pip install kivy-automate

# Usage

There is 2 part of the software: a server and a client. The server must be
installed into your application, and then you can use the client to communicate
with it.
## Install the server into your application

Just run this command to create an `automateserver.py` file that you can import:

    python -m automate.install

Then, in your main.py, just import `automateserver` when you want to start the
server automation asynchronously:

    import automateserver

## Use the client

It is as simple as:

    from automate.client import *

    connect()
    wait("app.screen_manager.current == 'welcome'")
    wait("app.screen_manager.current_screen.progression == 1")
    screenshot()
    quit()

This example will:

1. connect to the first started application, or wait the application to start
2. wait for our screen manager to show the "welcome" screen
3. wait the transition to be finished
4. at this point, a screenshot will be done
5. and the application will quit.

# Commands

#### connect(host="localhost", port=7777, timeout=10)

Connect to the specified host / port. It will wait at maximum 10 seconds before
going to timeout.

    connect("192.168.1.6", timeout=60)

### wait(cond, timeout=-1)

Wait for a condition to be True, or break when the timeout happen. The only
available symbol in the condition globals() is the current running application
instance, accessible from `app` keyword. If the condition have an exception
while running, it will  wait 100ms before retrying.

So no error will be throw if you don't write a valid condition, the only way to
get out is either the condition happen, either the timeout happen.

    wait("app.screen_manager.current == 'welcome'")

### execute(cmd)

Execute a python statement in the application context. The only available symbol
in the cmd globals() is the current running application instance, accessible
from `app` keyword.

    execute("app.screen_manager.switch_to('credits')")

### screenshot(name="screenshot", suffix=None, outdir=None)

Take a screenshot of the current displayed window. The screenshot will be saved into `outdir` (current directory if None), with the name of `{name}{suffix}.png`.
Care, it will replace any existing filename.

    screenshot("welcome")

### quit()

Quit the application. This is a shortcut to calling
`App.get_running_app().stop()`.

### spawn(...)

Context manager that can be used to run your application multiple times, like:

    for screen in ("onex", "droid2", "ipad"):
        with spawn("python", "main.py", "-m", "screen:{}".format(screen)):
            connect()
            wait()
            screenshot("startup", screen)
            wait("app.screen_manager.current == 'welcome'")
            screenshot("welcome", screen)
            quit()
