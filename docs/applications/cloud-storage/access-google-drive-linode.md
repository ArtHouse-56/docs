---
author:
    name: Scott Sumner
    email: scottinthebooth@gmail.com
description: 'Access Google Drive from your Linode console'
keywords: 'google,drive,console,fuse,apt,ubuntu'
license: '[CC BY-ND 3.0](http://creativecommons.org/licenses/by-nd/3.0/us/)'
published: 'Monday, September 28th, 2015'
modified: Monday, September 28th, 2015
modified_by:
    name: Linode
title: 'Access Google Drive from Linode'
contributor:
    name: Scott Sumner
---

*This is a Linode Community guide. [Write for us](/docs/contribute) and earn $250 per published guide.*

<hr>

If you've discovered Google Drive then you know that it can be an indispensible tool for moving files around.  While one of the standard counter-arguments is "just carry a flash drive" that works great until you need to add a file to your Linode.  Here's how to install and configure a great free piece of software to access your Google Drive from your Linode!

**Google-drive-ocamlfuse (ocamlfuse)** uses the Drive API to scan and access your Google Drive contents.  A majority of the steps are authorizing its use and applying that authorization to the copy running on your Linode.  Let's get started, I'll explain a little more along the way.

## Installing software

First we have to add the repository where ocamlfuse lives to our Linode.  Once that's done we update so the changes are visible to us and then install like normal.  Login to your Linode via SSH and do the following:

1.  Add the repository:

        sudo add-apt-repository ppa:alessandro-strada/ppa

2.  Update apt:

        sudo apt-get update

3.  Install the application:

        sudo apt-get install google-drive-ocamlfuse


## Setup access to the Google Drive API

Now we're going to enable API access to Google Drive and create a set of credentials.  These steps require a web browser on your local computer.

1.  Visit the API developer's console on your desktop at https://console.developers.google.com/project

    ![The Google Developers console.](/docs/assets/drive_console.png)

2.  Create a project. Click **Create Project**, then give the project a name and click **Create** again:

    ![The new project window.  I called my project Google Drive Linode](/docs/assets/API_console_new_project.png)

    It will take a moment to create the project, when its complete you'll arrive at the dashboard:

    [![The project "dashboard"](/docs/assets/API-dashboard-small.png)](/docs/assets/API_dashboard.png)

3.  Enable the Google Drive API. Click APIs & auth, then APIs when the menu expands.  You'll see a list like this.  Then click on **Drive API**:

    [![The API list.](/docs/assets/google_API_screen-small.png)](/docs/assets/google_API_screen.png)

    Click the blue **Enable API** button at the top of the page

    ![The Google Drive API description.](/docs/assets/drive_enable_API.png)

4.  Click **Credentials** in the menu.  Then click **add new credential**

    ![The credentials screen.](/docs/assets/new_oauth2.jpg)

    Click **Configure consent screen** to provide some information to Google.  Google assumes you are writing a piece of software so it wants some information about it.

    [![Creating a client ID.](/docs/assets/new_configure_screen_small.jpg)](/docs/assets/new_configure_screen.jpg)

    The product name field is required, you can leave everything else blank.  Then click **Save** at the bottom of the screen.

    ![Configuring the Google Drive API project.  I named mine "Google Drive Linode link"](/docs/assets/new_product_name.jpg)

    Now click **Other** for the application type.  Google will ask for a name again, I used the default.  Then click **Create**

    ![Selecting where this API key will be used](/docs/assets/new_other_application.jpg)

    You'll now have **Client ID** and **Client secret** strings:

    ![The Client ID and Client secret strings generated by google.](/docs/assets/new_credentials.jpg)

## Adding the Authorization to google-drive-ocamlfuse

Next, we'll provide the credentials to ocamlfuse, authorizing it to access your Google Drive.  Follow these steps in the SSH session to your Linode:

1.  Authorize your Google Drive link:

        google-drive-ocamlfuse -headless -label me -id `client ID from above` -secret `client secret from above`

    The output from this command will give you a long URL.  Copy it and paste it for use in step 2:
    
        Please, open the following URL in a web browser: https://accounts.google.com/o/oauth2/auth?client_id=URL_SNIPPED
        Please enter the verification code:

2.  Grant permission one more time. Google will ask for permission to allow this new application (ocamlfuse) to access your Google Drive.  Click **Accept** to receive the verification code:

    ![The permission request screen from google.](/docs/assets/google_authorization.png)

3.  Copy/paste the verification code back to ocamlfuse.


## Choose Where Google Drive Will Show Up

The following steps will create an empty directory where Google Drive will live.  All of your Google Drive files and folders will appear here.

1.  Create a mount point:

        mkdir googleDrive

2.  Mount your google drive:

        google-drive-ocamlfuse -label me googleDrive


## Wrapping Up

And you're done!  The directory **googleDrive** will now reflect your Google Drive contents!  The first time you access the folder it may take a few minutes for the contents to syncronize.  After that folder access is almost immediate.


## Resetting the connection

If Google Drive disappears,its likely that the credentials you've created have expired.  If this does occur:

1.  On your desktop visit **http://console.developers.google.com**

2.  Click **APIs & auth**

3.  Click **Credentials**

4.  Click **Reset secret**

5.  Repeat the steps in **Adding the Authorization to google-drive-ocamlfuse**

6.  Remount your Google Drive:

    google-drive-ocamlfuse -label me googleDrive